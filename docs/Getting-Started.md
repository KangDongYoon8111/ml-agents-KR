# Getting Started Guide

이 가이드는 Unity에서 제공하는 [예제 환경](Learning-Environment-Examples.md) 중 하나를 열고,
훈련된 모델을 Unity 환경에 통합하는 과정을 처음부터 끝까지 안내합니다.
이 튜토리얼을 읽고 나면, 예제 환경 중 어떤 환경이든 훈련할 수 있게 될 것입니다.
[Unity Engine](https://unity3d.com/unity)에 익숙하지 않다면, 
도움이 되는 팁을 제공하는 [Background: Unity](Background-Unity.md) 페이지를 확인해 보세요.
또한, 머신러닝에 익숙하지 않다면, 간략한 개요와 유용한 팁이 포함된 [Background: Machine Learning](Background-Machine-Learning.md) 페이지를 참조해 보세요.

![3D Balance Ball](images/balance.png)

이 가이드에서는 여러 개의 에이전트 큐브와 공(서로의 복사본들로 구성(프리팹))으로 이루어진 **3D Balance Ball** 환경을 사용합니다.
각 에이전트 큐브는 수평 또는 수직으로 회전하여 공이 떨어지지 않도록 유지하려고 합니다.
이 환경에서 에이전트 큐브는 공을 균형 있게 유지할 때마다 보상을 받는 **에이전트(Agent)** 역할을 합니다.
반면, 공을 떨어뜨리면 페널티로서 부정적인 보상을 받습니다. 
훈련 과정의 목표는 에이전트들이 공을 머리 위에 균형 있게 유지하는 방법을 학습하도록 하는 것 입니다.

시작해봅시다!

## Installation(설치)

아직 설치하지 않았다면, [설치 지침](Installation.md)을 따라주세요.
그 후, 모든 예제 환경이 포함된 Unity 프로젝트를 엽니다 :

1. 메뉴에서 `Window -> Package Manager`로 이동하여 패키지 관리자 창을 엽니다.
1. ML-Agents 패키지로 이동하여 클릭합니다.
1. `3D Ball` 샘플을 찾아 `Import` 버튼을 클릭합니다.
1. **Project** 창에서 `Assets/ML-Agents/Examples/3DBall/Scenes` 폴더로 이동하여 `3DBall` 씬 파일을 엽니다.


**DK Check Point:** 
1. 위 방식으로 접근 시 `3D Ball`항목이 0 KB 로 오류가 발생될 수 있음.
   git을 통해 내려받은 저장소 파일 내부 `ml-agents/com.unity.ml-agents/3DBall/3DBall.unitypackage` 로 대체가능.
1. 샘플 임포트시 **ModelOverrider.cs** 스크립트 파일로 인하여 에러가 발생될 수 있음.
   `Assets/ML-Agents/Examples/SharedAssets/Scripts` 폴더로 이동하여 `ModelOverrider.cs` 파일을 엽니다.
1. `using System.Reflection;` 추가 후, 에러가 발생되는 지점 `LoadSentisModel(byte[] rawModel)` 메서드로 이동하여,
   아래 내용으로 교체하세요.
```sh
    ModelAsset LoadSentisModel(byte[] rawModel)
        {
            
            var asset = ScriptableObject.CreateInstance<ModelAsset>();
           
            var modelAssetDataType = typeof(ModelAsset).Assembly.GetType("NamespaceOfModelAssetData.ModelAssetData"); 
            var modelAssetDataInstance = ScriptableObject.CreateInstance(modelAssetDataType);
     
            FieldInfo modelAssetDataField = typeof(ModelAsset).GetField("modelAssetData", BindingFlags.NonPublic | BindingFlags.Instance);

            if (modelAssetDataField != null)
            {
                modelAssetDataField.SetValue(asset, modelAssetDataInstance);

                FieldInfo valueField = modelAssetDataType.GetField("value", BindingFlags.Public | BindingFlags.NonPublic | BindingFlags.Instance);
                if (valueField != null)
                {
                    valueField.SetValue(modelAssetDataInstance, rawModel);
                }
                else
                {
                    Debug.LogError("Failed to retrieve the value field from ModelAssetData.");
                }
            }
            else
            {
                Debug.LogError("Failed to retrieve modelAssetData field from ModelAsset using reflection.");
            }

            return asset;
        }
```

## Understanding a Unity Environment(Unity 환경 이해하기)

에이전트(_agent_)는 환경(_environment_)을 관찰(_observes_)하고 상호작용하는 자율적인 행위자(_actor_)입니다.
Unity의 맥락에서 환경은 하나 이상의 에이전트 객체와 에이전트가 상호작용하는 다른 엔티티(_entities_)들이 포함된 씬을 의미합니다.

**DK Check Point:** 엔티티(_entities_)란, Unity에서 에이전트와 상호작용할 수 있는 모든 객체나 요소를 의미합니다.
이는 에이전트의 행동이나 학습에 영향을 미칠 수 있는 물리적 오브젝트, 환경의 특성, 또는 다른 NPC 등을 포함합니다.
즉, 에이전트가 목표를 달성하기 위해 고려해야 하거나 피드백을 받을 수 있는 환경 내 모든 요소를 포괄하는 개념입니다.

![Unity Editor](images/mlagents-3DBallHierarchy.png)

**Note:** In Unity, the base object of everything in a scene is the
_GameObject_. The GameObject is essentially a container for everything else,
including behaviors, graphics, physics, etc. To see the components that make up
a GameObject, select the GameObject in the Scene window, and open the Inspector
window. The Inspector shows every component on a GameObject.

The first thing you may notice after opening the 3D Balance Ball scene is that
it contains not one, but several agent cubes. Each agent cube in the scene is an
independent agent, but they all share the same Behavior. 3D Balance Ball does
this to speed up training since all twelve agents contribute to training in
parallel.

### Agent

The Agent is the actor that observes and takes actions in the environment. In
the 3D Balance Ball environment, the Agent components are placed on the twelve
"Agent" GameObjects. The base Agent object has a few properties that affect its
behavior:

- **Behavior Parameters** — Every Agent must have a Behavior. The Behavior
  determines how an Agent makes decisions.
- **Max Step** — Defines how many simulation steps can occur before the Agent's
  episode ends. In 3D Balance Ball, an Agent restarts after 5000 steps.

#### Behavior Parameters : Vector Observation Space

Before making a decision, an agent collects its observation about its state in
the world. The vector observation is a vector of floating point numbers which
contain relevant information for the agent to make decisions.

The Behavior Parameters of the 3D Balance Ball example uses a `Space Size` of 8.
This means that the feature vector containing the Agent's observations contains
eight elements: the `x` and `z` components of the agent cube's rotation and the
`x`, `y`, and `z` components of the ball's relative position and velocity.

#### Behavior Parameters : Actions

An Agent is given instructions in the form of actions.
ML-Agents Toolkit classifies actions into two types: continuous and discrete.
The 3D Balance Ball example is programmed to use continuous actions, which
are a vector of floating-point numbers that can vary continuously. More specifically,
it uses a `Space Size` of 2 to control the amount of `x` and `z` rotations to apply to
itself to keep the ball balanced on its head.

## Running a pre-trained model

We include pre-trained models for our agents (`.onnx` files) and we use the
[Sentis](Sentis.md) to run these models inside
Unity. In this section, we will use the pre-trained model for the 3D Ball
example.

1. In the **Project** window, go to the
   `Assets/ML-Agents/Examples/3DBall/Prefabs` folder. Expand `3DBall` and click
   on the `Agent` prefab. You should see the `Agent` prefab in the **Inspector**
   window.

   **Note**: The platforms in the `3DBall` scene were created using the `3DBall`
   prefab. Instead of updating all 12 platforms individually, you can update the
   `3DBall` prefab instead.

   ![Platform Prefab](images/platform_prefab.png)

1. In the **Project** window, drag the **3DBall** Model located in
   `Assets/ML-Agents/Examples/3DBall/TFModels` into the `Model` property under
   `Behavior Parameters (Script)` component in the Agent GameObject
   **Inspector** window.

   ![3dball learning brain](images/3dball_learning_brain.png)

1. You should notice that each `Agent` under each `3DBall` in the **Hierarchy**
   windows now contains **3DBall** as `Model` on the `Behavior Parameters`.
   **Note** : You can modify multiple game objects in a scene by selecting them
   all at once using the search bar in the Scene Hierarchy.
1. Set the **Inference Device** to use for this model as `CPU`.
1. Click the **Play** button in the Unity Editor and you will see the platforms
   balance the balls using the pre-trained model.

## Training a new model with Reinforcement Learning

While we provide pre-trained models for the agents in this environment, any
environment you make yourself will require training agents from scratch to
generate a new model file. In this section we will demonstrate how to use the
reinforcement learning algorithms that are part of the ML-Agents Python package
to accomplish this. We have provided a convenient command `mlagents-learn` which
accepts arguments used to configure both training and inference phases.

### Training the environment

1. Open a command or terminal window.
1. Navigate to the folder where you cloned the `ml-agents` repository. **Note**:
   If you followed the default [installation](Installation.md), then you should
   be able to run `mlagents-learn` from any directory.
1. Run `mlagents-learn config/ppo/3DBall.yaml --run-id=first3DBallRun`.
   - `config/ppo/3DBall.yaml` is the path to a default training
     configuration file that we provide. The `config/ppo` folder includes training configuration
     files for all our example environments, including 3DBall.
   - `run-id` is a unique name for this training session.
1. When the message _"Start training by pressing the Play button in the Unity
   Editor"_ is displayed on the screen, you can press the **Play** button in
   Unity to start training in the Editor.

If `mlagents-learn` runs correctly and starts training, you should see something
like this:

```console
INFO:mlagents_envs:
'Ball3DAcademy' started successfully!
Unity Academy name: Ball3DAcademy

INFO:mlagents_envs:Connected new brain:
Unity brain name: 3DBallLearning
        Number of Visual Observations (per agent): 0
        Vector Observation space size (per agent): 8
        Number of stacked Vector Observation: 1
INFO:mlagents_envs:Hyperparameters for the PPO Trainer of brain 3DBallLearning:
        batch_size:          64
        beta:                0.001
        buffer_size:         12000
        epsilon:             0.2
        gamma:               0.995
        hidden_units:        128
        lambd:               0.99
        learning_rate:       0.0003
        max_steps:           5.0e4
        normalize:           True
        num_epoch:           3
        num_layers:          2
        time_horizon:        1000
        sequence_length:     64
        summary_freq:        1000
        use_recurrent:       False
        memory_size:         256
        use_curiosity:       False
        curiosity_strength:  0.01
        curiosity_enc_size:  128
        output_path: ./results/first3DBallRun/3DBallLearning
INFO:mlagents.trainers: first3DBallRun: 3DBallLearning: Step: 1000. Mean Reward: 1.242. Std of Reward: 0.746. Training.
INFO:mlagents.trainers: first3DBallRun: 3DBallLearning: Step: 2000. Mean Reward: 1.319. Std of Reward: 0.693. Training.
INFO:mlagents.trainers: first3DBallRun: 3DBallLearning: Step: 3000. Mean Reward: 1.804. Std of Reward: 1.056. Training.
INFO:mlagents.trainers: first3DBallRun: 3DBallLearning: Step: 4000. Mean Reward: 2.151. Std of Reward: 1.432. Training.
INFO:mlagents.trainers: first3DBallRun: 3DBallLearning: Step: 5000. Mean Reward: 3.175. Std of Reward: 2.250. Training.
INFO:mlagents.trainers: first3DBallRun: 3DBallLearning: Step: 6000. Mean Reward: 4.898. Std of Reward: 4.019. Training.
INFO:mlagents.trainers: first3DBallRun: 3DBallLearning: Step: 7000. Mean Reward: 6.716. Std of Reward: 5.125. Training.
INFO:mlagents.trainers: first3DBallRun: 3DBallLearning: Step: 8000. Mean Reward: 12.124. Std of Reward: 11.929. Training.
INFO:mlagents.trainers: first3DBallRun: 3DBallLearning: Step: 9000. Mean Reward: 18.151. Std of Reward: 16.871. Training.
INFO:mlagents.trainers: first3DBallRun: 3DBallLearning: Step: 10000. Mean Reward: 27.284. Std of Reward: 28.667. Training.
```

Note how the `Mean Reward` value printed to the screen increases as training
progresses. This is a positive sign that training is succeeding.

**Note**: You can train using an executable rather than the Editor. To do so,
follow the instructions in
[Using an Executable](Learning-Environment-Executable.md).

### Observing Training Progress

Once you start training using `mlagents-learn` in the way described in the
previous section, the `ml-agents` directory will contain a `results`
directory. In order to observe the training process in more detail, you can use
TensorBoard. From the command line run:

```sh
tensorboard --logdir results
```

Then navigate to `localhost:6006` in your browser to view the TensorBoard
summary statistics as shown below. For the purposes of this section, the most
important statistic is `Environment/Cumulative Reward` which should increase
throughout training, eventually converging close to `100` which is the maximum
reward the agent can accumulate.

![Example TensorBoard Run](images/mlagents-TensorBoard.png)

## Embedding the model into the Unity Environment

Once the training process completes, and the training process saves the model
(denoted by the `Saved Model` message) you can add it to the Unity project and
use it with compatible Agents (the Agents that generated the model). **Note:**
Do not just close the Unity Window once the `Saved Model` message appears.
Either wait for the training process to close the window or press `Ctrl+C` at
the command-line prompt. If you close the window manually, the `.onnx` file
containing the trained model is not exported into the ml-agents folder.

If you've quit the training early using `Ctrl+C` and want to resume training,
run the same command again, appending the `--resume` flag:

```sh
mlagents-learn config/ppo/3DBall.yaml --run-id=first3DBallRun --resume
```

Your trained model will be at `results/<run-identifier>/<behavior_name>.onnx` where
`<behavior_name>` is the name of the `Behavior Name` of the agents corresponding
to the model. This file corresponds to your model's latest checkpoint. You can
now embed this trained model into your Agents by following the steps below,
which is similar to the steps described [above](#running-a-pre-trained-model).

1. Move your model file into
   `Project/Assets/ML-Agents/Examples/3DBall/TFModels/`.
1. Open the Unity Editor, and select the **3DBall** scene as described above.
1. Select the **3DBall** prefab Agent object.
1. Drag the `<behavior_name>.onnx` file from the Project window of the Editor to
   the **Model** placeholder in the **Ball3DAgent** inspector window.
1. Press the **Play** button at the top of the Editor.

## Next Steps

- For more information on the ML-Agents Toolkit, in addition to helpful
  background, check out the [ML-Agents Toolkit Overview](ML-Agents-Overview.md)
  page.
- For a "Hello World" introduction to creating your own Learning Environment,
  check out the
  [Making a New Learning Environment](Learning-Environment-Create-New.md) page.
- For an overview on the more complex example environments that are provided in
  this toolkit, check out the
  [Example Environments](Learning-Environment-Examples.md) page.
- For more information on the various training options available, check out the
  [Training ML-Agents](Training-ML-Agents.md) page.
