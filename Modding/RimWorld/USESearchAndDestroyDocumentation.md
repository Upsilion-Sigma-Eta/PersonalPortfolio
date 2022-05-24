# USESearchAndDestroy

## Meta data
---

ModName : USE Search And Destroy  
ModPackageName : USE.SearchAndDestroy  
Supported Game Version : 1.0-1.3  

---

## 설계  
### 요구 조건
1. 정착민들(Colonist, inGameCode : Pawn)들이 게임 코드 상으로 이미 구현된 전투 AI를 키고 끌 수 있어야 함.
2. 전투 AI 모드가 켜져 있더라도 유저가 수동으로 정착민을 컨트롤 할 수 있어야 함
3. 근접 전투 AI와 원거리 전투 AI를 분리해서 선택할 수 있도록 해야함. 마지막으로 선택한 AI 모드에 따라 전투를 진행할 수 있도록 해야함.

### 사용 예시(Use Case)
1. 근접 무기를 들고 있는 정착민 5명과 습격자 5명
---

1. 정착민들을 소집(Draft)한다.
2. 정착민들에게 근접 전투 AI를 활성화시키는 버튼을 누른다.
3. 정착민들이 근접 전투 AI에 따라서 타겟을 잡고, 해당 타겟에게 근접해 공격한다.
   1. 이 때, 동일 타겟을 목표로 잡을수도 있다.
4. 모든 습격자가 무력화될때까지 자동으로 타겟을 잡아서 공격한다. 모든 타겟이 쓰러지면 제자리에서 대기한다.

---
2. 원거리 무기를 들고 있는 정착민 5명과 습격자 5명
---

1. 정착민들을 소집한다.
2. 정착민들에게 원거리 전투 AI를 활성화시키는 버튼을 누른다.
3. 정착민들이 원거리 전투 AI에 따라서 타겟을 잡고, 해당 타겟을 공격할 수 있는 거리까지 이동한다.
4. 정착민들이 원거리 무기를 통해서 습격자들을 공격한다.(게임 내에서는 무기의 동사(혹은 동작, Verb)를 캐스트(Case)한다.)
5. 모든 타겟이 무력화되면 제자리에서 대기한다.

---
3. 근접 무기를 들고있는 정착민 5명, 원거리 무기를 들고 있는 정착민 5명, 습격자 10명
---

1. 정착민들을 소집한다.
2. 정착민들중 근접 무기를 든 정착민들은 근접 전투 AI를 활성화하는 버튼을 누른다.
3. 정착민들중 원거리 무기를 든 정착민들은 원거리 전투 AI를 활성화하는 버튼을 누른다.
4. 각 정착민들은 활성화된 전투 AI에 따라 근접 전투, 혹은 원거리 전투를 수행한다. (사용 예시 1과 2를 보시오.)
5. 모든 타겟(습격자)이 무력화되면 제자리에서 대기한다.

---

### 의사코드
- 근접 전투 AI 활성화시 기대하는 AI의 행동
```C#
Algorithm MeleeCombatAI
Input: Pawn pawn
Output: Job job

public Job MeleeCombatAI
{
    GetTargetForPawn(pawn)

    Job job - null;

    if (pawn.mindstate.enemyTarget != null) {
        LocalTargetInfo localTarget = pawn.mindstate.enemyTarget;
        job = JobMaker.MakeJob(JobDefOf.Goto, enemyTarget);
    }

    return job;
}
```