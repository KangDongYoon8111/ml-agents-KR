# Installation(설치)

ML-Agents Toolkit은 여러 구성 요소로 이루어져 있습니다.:

- Unity 패키지 ([`com.unity.ml-agents`](../com.unity.ml-agents/))는 Unity 프로젝트에 통합될 Unity C# SDK를 포함하고 있습니다. 이 패키지에는 ML-Agents를 시작하는 데 도움이 되는 샘플이 포함되어 있습니다.
- Unity 패키지
  ([`com.unity.ml-agents.extensions`](../com.unity.ml-agents.extensions/))는 아직 기본 `com.unity.ml-agents`
  패키지의 일부가 되지 않은 실험적인 C#/Unity 구성 요소를 포함하고 있습니다.
  `com.unity.ml-agents.extensions`는 `com.unity.ml-agents`에 직접 의존성을 가집니다.
- 2개의 Python 패키지:
  - [`mlagents`](../ml-agents/)는 Unity 장면에서 행동을 훈련할 수 있게 해주는 머신 러닝 알고리즘을 포함하고 있습니다.
    ML-Agents의 대부분 사용자들은 `mlagents`를 직접 설치하면 충분합니다.
  - [`mlagents_envs`](../ml-agents-envs/)는 Unity 장면과 상호작용할 수 있는 Python API 세트를 포함하고 있습니다.
    이는 Unity 장면과 Python 머신 러닝 알고리즘 간의 데이터 메시징을 용이하게 하는 기본 레이어입니다. 따라서 `mlagents`는
    `mlagents_envs`에 의존합니다.
- Unity [Project](https://github.com/Unity-Technologies/ml-agents/tree/main/Project/)는 툴킷의 다양한 기능을 강조하는
  여러 [example environments(예제 환경)](Learning-Environment-Examples.md)을 포함하고 있어 시작하는 데 도움을 줍니다.

따라서 ML-Agents Toolkit을 설치하고 사용하기 위해서는 다음이 필요합니다:

- Unity 설치 (2023.2 이상)
- Python 설치 (>= 3.10.1, <=3.10.12) 3.10.12 버전을 사용하는 것을 권장합니다.
- Clone this repository (Recommended for the latest version and bug fixes)
  - 참고: 저장소를 클론하지 않으면 예제 환경, 훈련 구성 및 com.unity.ml-agents.extensions 패키지에 접근할 수 없습니다. 또한,
  [Getting Started Guide](Getting-Started.md)는 저장소를 클론한 것으로 가정하고 있습니다.
- `com.unity.ml-agents` Unity 패키지
- `com.unity.ml-agents.extensions` Unity 패키지 (선택 사항).
- `mlagents-envs` 설치
- `mlagents` Python 패키지 설치

### Install **Unity 2023.2** or Later(Unity 2023.2 이상을 설치)

Unity를 [Download(다운로드)](https://unity3d.com/get-unity/download)하고 설치하세요. Unity Hub를 통해 설치하는 것을 강력히 권장합니다. 
이렇게 하면 여러 Unity 버전을 관리할 수 있습니다.

### Install **Python 3.10.12**(Python 3.10.12 설치)

Python 3.10.12를 [installing(설치)](https://www.python.org/downloads/)하는 것을 권장합니다.
Windows를 사용하는 경우 x86이 아닌 x86-64 버전을 설치하세요. Python 환경에 `pip3`가 포함되어 있지 않다면
[instructions(설치 방법)](https://packaging.python.org/guides/installing-using-linux-tools/#installing-pip-setuptools-wheel-with-linux-package-managers)을 참조하세요. 또한, Python 가상 환경 관리를 위해 [conda](https://docs.conda.io/en/latest/) 나 [mamba](https://github.com/mamba-org/mamba)를 사용하는 것을 권장합니다.

#### Conda python setup(Conda를 사용한 Python 설치)

Conda가 시스템에 설치된 후, 터미널을 열고 다음 명령어를 실행하여 Python 3.10.12 가상 환경을 설정하고 활성화 하세요.

```shell
conda create -n mlagents python=3.10.12 && conda activate mlagents
```

### Clone the ML-Agents Toolkit Repository (Recommended) (ML-Agents Toolkit 저장소를 복제하세요. (권장))

Unity와 Python을 설치했으니, 이제 Unity와 Python 패키지를 설치할 수 있습니다. 
이러한 패키지를 설치하기 위해 저장소를 복제할 필요는 없지만, 예제 환경과 훈련 구성을 다운로드하여 실험해보고 싶다면 저장소를 클론할 수 있습니다
(일부 튜토리얼/가이드는 예제 환경에 접근할 수 있다고 가정합니다).

**NOTE:** Unity 패키지에 샘플이 포함되어 있습니다. 더 많은 예제를 탐색하고 싶다면 저장소를 복제할 필요가 있습니다.

```sh
git clone --branch release_22 https://github.com/Unity-Technologies/ml-agents.git
```

`--branch release_22` 옵션은 최신 안정 버전의 태그로 전환합니다. 
이 옵션을 생략하면 잠재적으로 불안정한 `develop` 브랜치가 선택됩니다. 그러나 릴리스 브랜치가 작동하지 않는 경우, 
버그 및 의존성 문제에 대한 잠재적인 수정 사항이 있을 수 있으므로 `develop` 브랜치를 사용하는 것이 권장됩니다.

(Optional to get bleeding edge(최신 버전을 받기 위한 선택 사항))

```sh
git clone --branch https://github.com/Unity-Technologies/ml-agents.git
```

#### Advanced: Local Installation for Development (고급 : 개발을 위한 로컬 설치)

You will need to clone the repository if you plan to modify or extend the
ML-Agents Toolkit for your purposes. If you plan to contribute those changes
back, make sure to clone the `develop` branch (by omitting `--branch release_22`
from the command above). See our
[Contributions Guidelines](../com.unity.ml-agents/CONTRIBUTING.md) for more
information on contributing to the ML-Agents Toolkit.

### Install the `com.unity.ml-agents` Unity package

The Unity ML-Agents C# SDK is a Unity Package. You can install the
`com.unity.ml-agents` package
[directly from the Package Manager registry](https://docs.unity3d.com/Manual/upm-ui-install.html).
Please make sure you enable 'Preview Packages' in the 'Advanced' dropdown in
order to find the latest Preview release of the package.

**NOTE:** If you do not see the ML-Agents package listed in the Package Manager
please follow the [advanced installation instructions](#advanced-local-installation-for-development) below.

#### Advanced: Local Installation for Development

You can [add the local](https://docs.unity3d.com/Manual/upm-ui-local.html)
`com.unity.ml-agents` package (from the repository that you just cloned) to your
project by:

1. navigating to the menu `Window` -> `Package Manager`.
1. In the package manager window click on the `+` button on the top left of the packages list).
1. Select `Add package from disk...`
1. Navigate into the `com.unity.ml-agents` folder.
1. Select the `package.json` file.

<p align="center">
  <img src="../images/unity_package_manager_window.png"
       alt="Unity Package Manager Window"
       height="150"
       border="10" />
  <img src="../images/unity_package_json.png"
     alt="package.json"
     height="150"
     border="10" />
</p>

If you are going to follow the examples from our documentation, you can open the
`Project` folder in Unity and start tinkering immediately.

### Install the `com.unity.ml-agents.extensions` Unity package (Optional)

To install the `com.unity.ml-agents.extensions` package, you need to first
clone the repo and then complete a local installation similar to what was
outlined in the previous
[Advanced: Local Installation for Development](#advanced-local-installation-for-development-1)
section. Complete installation steps can be found in the
[package documentation](../com.unity.ml-agents.extensions/Documentation~/com.unity.ml-agents.extensions.md#installation).

### Install the `mlagents` Python package

Installing the `mlagents` Python package involves installing other Python
packages that `mlagents` depends on. So you may run into installation issues if
your machine has older versions of any of those dependencies already installed.
Consequently, our supported path for installing `mlagents` is to leverage Python
Virtual Environments. Virtual Environments provide a mechanism for isolating the
dependencies for each project and are supported on Mac / Windows / Linux. We
offer a dedicated [guide on Virtual Environments](Using-Virtual-Environment.md).

#### (Windows) Installing PyTorch

On Windows, you'll have to install the PyTorch package separately prior to
installing ML-Agents in order to make sure the cuda-enabled version is used,
rather than the CPU-only version. Activate your virtual environment and run from
the command line:

```sh
pip3 install torch~=2.2.1 --index-url https://download.pytorch.org/whl/cu121
```

Note that on Windows, you may also need Microsoft's
[Visual C++ Redistributable](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads)
if you don't have it already. See the [PyTorch installation guide](https://pytorch.org/get-started/locally/)
for more installation options and versions.

#### (OS X) Installing GRPC libraries

On OS X, you may need to explicitly install the GRPC runtime libraries to avoid hitting errors when training like `dlopen(/Users/alex.mccarthy/miniconda3/envs/mlagents/lib/python3.10/site-packages/grpc/_cython/cygrpc.cpython-310-darwin.so, 0x0002): symbol not found in flat namespace '_CFRelease'`.

```sh
pip3 install grpcio
```

#### Installing `mlagents`

To install the `mlagents` Python package, activate your virtual environment and
run from the command line:

```sh
cd /path/to/ml-agents
python -m pip install ./ml-agents-envs
python -m pip install ./ml-agents
```

Note that this will install `mlagents` from the cloned repository, _not_ from the PyPi
repository. If you installed this correctly, you should be able to run
`mlagents-learn --help`, after which you will see the command
line parameters you can use with `mlagents-learn`.

**NOTE:** Since ML-Agents development has slowed, PyPi releases will be less frequent. However, you can install from PyPi by executing
the following command:

```shell
python -m pip install mlagents==1.1.0
```

which will install the latest version of ML-Agents and associated dependencies available on PyPi. Note, you need to have the matching version of
the Unity packages with the particular release of the python packages. You can find the release history [here](https://github.com/Unity-Technologies/ml-agents/releases)

By installing the `mlagents` package, the dependencies listed in the
[setup.py file](../ml-agents/setup.py) are also installed. These include
[PyTorch](Background-PyTorch.md).

#### Advanced: Local Installation for Development

If you intend to make modifications to `mlagents` or `mlagents_envs`, you should
install the packages from the cloned repository rather than from PyPi. To do
this, you will need to install `mlagents` and `mlagents_envs` separately. From
the repository's root directory, run:

```sh
pip3 install torch -f https://download.pytorch.org/whl/torch_stable.html
pip3 install -e ./ml-agents-envs
pip3 install -e ./ml-agents
```

Running pip with the `-e` flag will let you make changes to the Python files
directly and have those reflected when you run `mlagents-learn`. It is important
to install these packages in this order as the `mlagents` package depends on
`mlagents_envs`, and installing it in the other order will download
`mlagents_envs` from PyPi.

## Next Steps

The [Getting Started](Getting-Started.md) guide contains several short tutorials
on setting up the ML-Agents Toolkit within Unity, running a pre-trained model,
in addition to building and training environments.

## Help

If you run into any problems regarding ML-Agents, refer to our [FAQ](FAQ.md) and
our [Limitations](Limitations.md) pages. If you can't find anything please
[submit an issue](https://github.com/Unity-Technologies/ml-agents/issues) and
make sure to cite relevant information on OS, Python version, and exact error
message (whenever possible).
