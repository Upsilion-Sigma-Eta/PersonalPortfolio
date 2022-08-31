# Brutal Doom 파생 모드들의 Fatality 구현

각 몬스터마다 Death.Fatality가 존재한다. Death.Fatality는 데미지 타입이 Fatality인 공격으로 몬스터가 죽었을 때 호출된다. 여기서 `TNT1 A 0 A_JumpIfIntargetInventory("FistsSelected", 1, 1)`라는 구문으로 만약 선택된 무기가 주먹이 아니면 다른 죽음의 코드로 이동한다. 만약 주먹이 선택되어 있었다면 Fatality를 실행한다.

이상하게도, Project Brutality 3.0에서는 버서커 모드나 악마 룬(코드 상에서는 PowerStrength라고 되어 있다.)을 먹어야만 실행 가능한데, Death.Fatality에는 악마 룬을 먹었는지에 대한 검사가 존재하지 않는다. 추측컨데, 이는 Fatality 데미지 타입을 가진 공격이 한정적이기 때문에 별도로 추가할 필요성을 느끼지 못한 것 같다.

Brutal Doom의 파생모드인 Brutal Doom Black Edition에서는 `countinv("FinisherToken",AAPTR_PLAYER_GETTARGET)>=1 && ` 라는 조건으로 페이탈리티(글로리킬)의 실행 여부를 판단한다. FinishToken은 Wound State에서 각 몬스터에게 부여된다. Project Brutality 3.0의 애드온인 GloryKill 모드에서도 동일한 방법을 채택하고 있다.

원본 Brtual Doom 에서는 어떻게 처리하는지에 대해서는 Brutal Doom의 신버전이 나오고 난 뒤 분석하고자 한다.