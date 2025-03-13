RL-DWA가 아니더라도 강화학습을 진행하면 좋을 것같은데 뭘 해야할지 모르겠음

# Reference
[RL-DWA](https://github.com/BlackTea12/RL-DWA)
# 수행해야할 목표
참조한 RL-DWA는 2D Pygame 시뮬레이션을 활용하여 강화학습을 수행합니다. 이를 실외에서 주행을 할 Husky A200에 적용하려합니다.
## Simulation 환경
- [Husky Gazebo](https://clearpathrobotics.com/blog/2020/07/clearpath-robots-get-new-gazebo-simulation-environments/) 
- [cpr_gazebo](https://github.com/clearpathrobotics/cpr_gazebo)
- OS1-32 LiDAR 센서를 Husky 모델에 추가하는 URDF 설정 파일을 생성
- 강화학습을 위한 OpenAI Gym 환경 구성

- [ouster-ros](https://github.com/clearpathrobotics/ouster-ros)

## 강화학습 프레임워크
1. 상태 공간(stat space) 정의
OS1-32 LiDAR 데이터를 기반으로 상태 공간을 정의합니다.
```
state = [
    lidar_data,            # OS1-32에서 얻은 거리 정보
    robot_velocity,        # 로봇의 선속도, 각속도
    target_relative_pos    # 목표 지점까지의 상대 위치(x, y)
]
```
2. 행동 공간(Action Space) 정의
Husky A200은 차동 구동 방식
```
action = [linear_velocity, angular_velocity] #연속적인 값
```
3. 보상 함수(Reward Function) 설계
```
reward = r_goal + r_collision + r_progress + r_smooth
```
- r_goal: 목표 지점에 도달했을 때 높은 양의 보상
- r_collision: 장애물과 충돌 시 큰 음의 보상
- r_progress: 목표 지점에 가까워질수록 작은 양의 보상
- r_smooth: 급격한 속도 변화에 대한 작은 음의 보상



시뮬레이션과 실제 환경 간의 차이를 줄이기 위해 동적 요소(관성, 마찰)를 고려한 Gazebo 시뮬레이션에서 추가 학습[link](https://pmc.ncbi.nlm.nih.gov/articles/PMC8493784/)
OpenAI Gym 인터페이스를 따르는것이 좋다.





## 시뮬레이션 최적화
- 시뮬레이션 매개변수 조정 
```
<max_step_size>0.001</max_step_size>
<real_time_update_rate>0</real_time_update_rate>
```
- GUI 비활성화하여 성능 향상
- 위와 같은 설정으로 속도를 15배까지 높일수 있다고 한다.[Link](https://karelics.fi/blog/2021/02/15/using-gazebo-for-reinforcement-learning/)


Husky 패키지
```
sudo apt-get install ros-[ROS버전]-husky-desktop
```

