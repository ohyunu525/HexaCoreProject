# HexaCoreProject

Unity에서 6족 Spiderbot의 자율 탐색을 시뮬레이션하는 프로젝트입니다. 보행·계단 학습은 [RLForHexapod](RLForHexapod)의
Isaac Lab 3.0 환경과 RSL-RL 설정을 사용합니다.

## 6족 자율 탐색 빠른 시작

1. Unity Hub에서 Unity `6000.5.3f1`로 `DKSH SW Project` 폴더를 엽니다.
2. `Assets/Scenes/LidarScene.unity` 씬을 열고 Play를 누릅니다.

이 씬은 Frontier 탐지·군집화·목표 선택, 8방향 A* 경로 계획,
`CharacterController` 기반 경로 추종을 Unity 안에서 실행하도록 구성되어 있습니다.

`ros2/`에는 ROS2/Nav2 연동을 위한 실험용 구성이 들어 있습니다.
Unity 독립 실행에는 ROS2 설치나 컨테이너 기동이 필요하지 않습니다.
[ROS2 연동 구성](ros2/README.md)

## CAD 설계와 개조

[HEXAPOD Onshape 조립 모델](https://cad.onshape.com/documents/f646c0fde8bd91cfa43cbae7/w/279609403375cff4404c6eae/e/76a6985095c8e8f19d67aec6)에서 로봇의 부품과 관절 구성을 확인할 수 있습니다.
저장소에는 URDF, STL 메시와 Unity 프리팹도 포함되어 있습니다. 사용할 파일과 좌표계,
수정한 모델을 시뮬레이션에 반영하는 방법은 [CAD 개조 안내](isaaclab_project/docs/cad_update_workflow.md)를 참고하세요.

## RLForHexapod 학습

학습 코드는 [RLForHexapod 원격 main의 dcbad6d](https://github.com/qorhvma/RLForHexapod/tree/dcbad6dc015cbe72d0a362a417b83a0c70713ecf)에서 가져왔습니다.
Python 3.12와 uv를 사용합니다. 기본 학습 환경은 Isaac Lab 3.0의 Newton/MJWarp를 사용합니다.
로봇과 계단의 USD 자산을 받으려면 Git LFS가 필요합니다.
의존성 버전은 [프로젝트 설정](RLForHexapod/pyproject.toml)과 잠금 파일에 고정되어 있습니다.

```powershell
git lfs pull
cd RLForHexapod
uv sync --locked
uv run --locked isaaclab list_envs
uv run --locked isaaclab train --rl_library rsl_rl --task Hexapod-Walk --num_envs 4
```

계단 teacher는 `Hexapod-Stair`로 학습합니다. Student 환경과 평가 옵션은
[작업 등록](RLForHexapod/src/RLforHexapod/tasks/walk/config/hexapod/__init__.py)과
[계단 평가 스크립트](RLForHexapod/scripts/eval_stairs.py)를 참고하세요.
`isaaclab_project`에는 기존 Unity 모델과 Raspberry Pi에서 사용하는 배포용 자산 및 실행 기록이 남아 있습니다.
기존 체크포인트를 이어서 학습하기 전에 새 환경의 로봇 모델과 관측·행동 계약을 확인하세요.
