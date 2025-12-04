---
layout: single
title:  "Trailer System Kinematic (작성中)"
---

### Trailer System Dynamics
트레일러가 연결된 차량은 일반 차량보다 조향과 자세 변화가 더 복잡하며, 특히 후진 시에는 동역학적 이해가 필수적이다.  
이번 글에서는 차량–트레일러 조합의 기본적인 기구학(Kinematics) 과 Yaw 동역학, 그리고 **차량·트레일러 연결부(coupling point)** 에서의 속도 관계를 이해하기 쉽게 정리한다.  
> Werling, Moritz, et al. "Reversing the general one-trailer system: Asymptotic curvature stabilization and path tracking." IEEE Transactions on intelligent transportation systems 15.2 (2013): 627-636.  

### Assumptions
모델을 유도하기 앞서 아래의 가정이 적용된다.  
후진의 경우 
차량은 강체로 가정
-> 모든 점이 동일한 각속도 ω를 가져야 하기 때문에
-> 회전하는 강체의 모든 지점은 동일한 회전 중심을 가짐

트레일러는 일반적으로 낮은 횡가속(Lateral acceleration) 상태에서 후진 및 저속 주행이 이루어진다.
따라서 타이어 슬립각(slip angle) 은 무시할 수 있으며, 차량과 트레일러 바퀴는 모두 순수 굴림(pure rolling) 을 가정한다 즉 휠의 방향으로 속도 벡터가 정의되며 이 가정을 기반으로 차량과 트레일러의 회전 중심(ICR, Instantaneous Center of Rotation)이 정의

### Vehicle Kinematic Model
차량을 강체로 가정하면 회전운동을 할때 모든 지점에서의 raw rate은 동일하며 회전 중심이 동일하다
차량의 Yaw Dynamic은 아래와 같이 주어진다. (글 참조) 조향각이 주어졌을때 차량의 회전 반경은 기하학적 관계에 따라 아래와 같이 정의된다. 이때 회전반경*각속도 = 선속도 이므로 아래와 같이 유도할 수 있다. 차량의 거동에 따른 Trailer의 Kinematic 모델을 유도할 것이다. 이때 Trailer는 차량의 

### Vehicle - Trailer Kinematic Model
트레일러가 차량에 연결되어 있는 경우, 차량의 움직임을 기반으로 트레일러의 운동을 기술하고자 한다.  
즉, 모델을 통해 알고자하는 하는것은 차량의 vx와 조향각을 알고 있을때 Trailer의 움직임을 기술하는 것. 즉 트레일러의 차속과 Yaw rate을 차량에 대한 parameter를 활용하여 표현하는 것  
트레일러의 yaw 동역학을 차량의 model과 커플링하기 위해 , 차량의 후축으로부터 거리 l_c > 0 만큼 떨어진 트레일러 연결점(coupling point)을 사용할것이다.

차량을 강체(rigid body)로 가정하면, 차량 후륜 중심에서의 속도 벡터(Translational velocity) 와 Yaw rate(Angular velocity) 를 이용하여, 강체 내 임의의 지점(= coupling point)의 속도를 차량 파라미터에 기반한 관계식으로 계산할 수 있다.  
동일한 방식으로 같은 coupling point의 속도를 트레일러 중심 기준에서 트레일러 기준 parameter로 표현할 수 있으며, 이 두 속도가 같다는 조건을 적용하면 차량의 운동과 트레일러의 운동 사이의 상관관계를 유도할 수 있다.

※  Rigid Motion Translation & Rotation

### Steering Actuator Model
본 논문에서는 스티어링 엑츄에이터의 시간 지연을 반영하기 위해 이를 1차 model로 근사화하여 모델링한다. 


1Track Trailer의 경우 휠에 슬립이 없지만, 2Track Trailer의 경우 슬립을 고려해야할 것 같은데?

Coupler 
트레일러 연결점(coupling point) r_c에서의 속도 벡터 v_c를 사용한다.

트레일러 기준 (vt,)

본 논문에서는 제어기를 구현하기 위해 

### Time Transformation