---
layout: post
title: "AI와 함께하는 수질 모니터링 시스템 구축기"
date: 2025-07-13
categories: [ROS2, AI]
excerpt: "ROS2 기반 수질 모니터링 시스템 구축 중 부동소수점 정밀도 문제 해결기"
---

# AI와 함께하는 수질 모니터링 시스템 구축기: ROS2 + MQTT + 부동소수점 정밀도 문제 해결

## 들어가며

안녕하세요! 저는 수질 모니터링 시스템을 개발하는 엔지니어입니다. 최근 AI 어시스턴트와 함께 ROS2 기반 수질 모니터링 시스템을 구축하면서 겪은 흥미로운 문제들과 해결 과정을 공유하고자 합니다.

특히 부동소수점 정밀도 문제와 수질 안정화 모니터링 기능 구현 과정에서 AI와의 협업이 얼마나 효과적이었는지 경험해보실 수 있을 것입니다.

## 🎯 프로젝트 개요

### 시스템 구성
- **ROS2 Foxy**: 로봇 운영체제
- **MQTT**: 실시간 데이터 통신
- **수질 측정기**
- **채수 시스템**
- **깊이 센서**: Modbus 통신

### 핵심 기능
1. **자동 수질 측정**: 지정된 깊이에서 수질 데이터 수집
2. **수질 안정화 모니터링**: 펌프 작동 시간 기반 안정화 판단
3. **실시간 데이터 처리**: 센서 데이터 통합 및 분석
4. **원격 제어**: MQTT를 통한 원격 시스템 제어

## 🚨 첫 번째 문제: 부동소수점 정밀도의 함정

### 문제 발견

깊이 센서 데이터를 처리하는 중에 이상한 현상을 발견했습니다.

```bash
$ ros2 topic echo /sample_depth
data: 0.6000000238418579
data: 0.6000000238418579
data: 0.6000000238418579
```

단순히 `0.6m`를 표현하려고 했는데, 왜 이렇게 긴 소수점이 나타날까요?

### AI와의 첫 번째 대화

**🤖 AI**: "Float32와 Float64의 차이점을 설명드리겠습니다..."

**👨‍💻 나**: "그럼 Float64 사용하는 게 낫겠네요!"

**🤖 AI**: "네, 맞습니다! Float64 사용이 표준이고 권장사항입니다!"

### 문제 분석

#### Float32 vs Float64 비교

```python
import numpy as np

# Float32 사용 시
f32 = np.float32(0.6)
print(f32)  # 0.6000000238418579

# Float64 사용 시  
f64 = np.float64(0.6)
print(f64)  # 0.6
print(round(f64, 2))  # 0.6
```

#### 비트 구조 차이
- **Float32**: 23비트 가수부 (약 7자리 십진수 정밀도)
- **Float64**: 52비트 가수부 (약 15-17자리 십진수 정밀도)

### 해결 과정

#### 1단계: 메시지 타입 변경

```python
# 변경 전
from std_msgs.msg import Float32
self.depth_publisher_ = self.create_publisher(Float32, 'sample_depth', 10)

# 변경 후
from std_msgs.msg import Float64
self.depth_publisher_ = self.create_publisher(Float64, 'sample_depth', 10)
```

#### 2단계: 반올림 적용

```python
# 깔끔한 소수점 표시
depth_m = round(current_depth / 100.0, 2)  # cm → m 변환
depth_msg = Float64(data=depth_m)
```

#### 3단계: 모든 노드 동기화
- `mqtt_reel_ctrl.py`: 퍼블리셔 변경
- `sensor_data_node.py`: 구독자 변경  
- `manta_data_processor.py`: 구독자 변경

### 결과

```bash
$ ros2 topic echo /sample_depth
data: 0.6
data: 0.6
data: 0.6
```

**완벽!** 이제 깊이 데이터가 깔끔하게 표시됩니다.

---

*이 글은 실제 ROS2 프로젝트에서 겪은 경험을 바탕으로 작성되었습니다. AI와의 협업이 얼마나 효과적인지 경험해보시길 바랍니다!*

**#ROS2 #AI #부동소수점 #수질모니터링 #MQTT #개발팁** 
