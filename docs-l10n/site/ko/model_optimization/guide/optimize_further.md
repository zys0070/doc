# 추가 최적화

사전 최적화된 모델과 훈련 후 도구가 사용 사례를 충족하지 못할 때의 다음 단계는 다른 훈련 시간 도구를 사용해 보는 것입니다.

훈련 시간 도구는 모델이 최적화 기술에 의한 변경 사항에 "적응"할 수 있도록 훈련 데이터에 대한 모델의 손실 함수를 이용합니다.

훈련 API를 사용하기 위한 시작점은 Keras 훈련 스크립트이며, 훈련 스크립트를 사전 훈련된 Keras 모델에서 선택적으로 초기화하여 추가 미세 조정할 수 있습니다.

시도해 볼 수 있는 훈련 시간 도구:

- [가중치 잘라내기](./pruning/)
- [양자화](./quantization/training)
- [가중치 클러스터링](./clustering/)
- [공동 작업 최적화](./combine/collaborative_optimization)
