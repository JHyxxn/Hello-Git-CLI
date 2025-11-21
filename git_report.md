# Git 실습 레포트: Merge 구조 생성 및 선형 히스토리 변환

## 서론

### 1-1. 실습 목적
본 실습은 Git을 사용하여 로컬 main 브랜치와 원격 저장소의 origin/main 브랜치가 분기된 후 merge 커밋으로 합치는 구조를 생성하고, 이후 merge 커밋을 제거하여 선형 히스토리로 변환하는 과정을 학습하는 것을 목적으로 합니다.

### 1-2. 실습 조건
- 브랜치는 생성하지 않고 main 브랜치만 사용
- 모든 작업은 main 브랜치에서 수행

### 1-3. 초기 상태
실습 시작 시점의 Git 히스토리 구조는 다음과 같습니다:
```
* b35cb3e (feature1) 새로운 기능 1 추가
* 25298f6 (hotfix) hotfix 실습
* a30aa56 (tag: v0.1) mybranch1 두 번째 커밋
* 07b88a0 mybranch1 첫 번째 커밋
* c66c637 두 번째 커밋
* 25d9eab 첫 번째 커밋
```

## 본론

### 2-1. 파일 생성 및 커밋

#### 2-1-1. "20235351" 커밋 생성 (원격 저장소용)
로컬에서 "20235351_김지현.txt" 파일을 생성하고 커밋합니다.

```bash
echo "20235351_김지현" > 20235351_김지현.txt
git add 20235351_김지현.txt
git commit -m "20235351"
```

- 결과: `57667c4 20235351` 커밋 생성
- 파일: `20235351_김지현.txt` (내용: "20235351_김지현")

#### 2-1-2. 원격 저장소에 push
생성한 커밋을 원격 저장소에 업로드합니다.

```bash
git push origin main
```

#### 2-1-3. "김지현" 커밋 생성 (로컬)
공통 조상으로 되돌린 후 로컬에서 다른 커밋을 생성합니다.

```bash
git reset --hard b35cb3e
echo "김지현_20235351" > 김지현_20235351.txt
git add 김지현_20235351.txt
git commit -m "김지현"
```

- 결과: `57f958e 김지현` 커밋 생성
- 파일: `김지현_20235351.txt` (내용: "김지현_20235351")

### 2-2. Merge 커밋 생성

#### 2-2-1. 원격 저장소 정보 갱신
```bash
git fetch origin
```

#### 2-2-2. Pull로 merge 커밋 생성
로컬 main과 origin/main이 분기된 상태에서 pull을 수행하면 자동으로 merge 커밋이 생성됩니다.

```bash
git pull
```

**이 시점의 히스토리 구조:**
```
*   f76b79a (HEAD -> main) Merge branch 'main' of https://github.com/JHyxxn/Hello-Git-CLI
|\  
* | 57f958e 김지현
| * 57667c4 (origin/main, origin/HEAD) 20235351
|/  
* b35cb3e (feature1) 새로운 기능 1 추가
```

### 2-3. 선형 히스토리로 변환

#### 2-3-1. 공통 조상으로 되돌리기
```bash
git reset --hard b35cb3e
```

#### 2-3-2. "20235351" 커밋 적용
```bash
git cherry-pick 57667c4
```
- 결과: `f63e53f 20235351` 커밋 생성

#### 2-3-3. "김지현" 커밋 적용
```bash
git cherry-pick 57f958e
```
- 결과: `dc7d0ac 김지현` 커밋 생성

#### 2-3-4. 원격 저장소 업데이트
```bash
git push origin main -f
```
- 주의: `-f` 옵션으로 force push (기존 히스토리 덮어쓰기)

#### 2-3-5. origin/HEAD 제거
```bash
git remote set-head origin -d
```

### 2-4. 사용된 주요 Git 명령어

| 명령어 | 목적 |
|--------|------|
| `git add <file>` | 파일을 스테이징 영역에 추가 |
| `git commit -m "<message>"` | 커밋 생성 |
| `git push origin main` | 원격 저장소에 push |
| `git reset --hard <commit>` | 특정 커밋으로 되돌리기 |
| `git fetch origin` | 원격 저장소 정보 갱신 |
| `git pull` | 원격 저장소에서 가져와 병합 |
| `git cherry-pick <commit>` | 특정 커밋을 현재 위치에 적용 |
| `git push origin main -f` | 원격 저장소에 force push |
| `git remote set-head origin -d` | origin/HEAD 참조 제거 |
| `git log --oneline --all --graph --decorate` | 히스토리 확인 |

## 결론

### 3-1. 최종 결과

#### 3-1-1. Git 히스토리 구조
```
* dc7d0ac (HEAD -> main, origin/main) 김지현
* f63e53f 20235351
* b35cb3e (feature1) 새로운 기능 1 추가
* 25298f6 (hotfix) hotfix 실습
* a30aa56 (tag: v0.1) mybranch1 두 번째 커밋
* 07b88a0 mybranch1 첫 번째 커밋
* c66c637 두 번째 커밋
* 25d9eab 첫 번째 커밋
```

#### 3-1-2. 파일 구조
- `김지현_20235351.txt`: 내용 "김지현_20235351"
- `20235351_김지현.txt`: 내용 "20235351_김지현"

#### 3-1-3. 주요 특징
- 선형 히스토리: merge 커밋 없이 모든 커밋이 순차적으로 배치
- 브랜치 미생성: 조건에 따라 main 브랜치만 사용
- 원격 동기화: main과 origin/main이 같은 커밋을 가리킴

### 3-2. 배운 점
1. Merge 커밋 생성: 로컬과 원격이 분기된 상태에서 pull하면 자동으로 merge 커밋 생성
2. Merge 커밋 제거: `cherry-pick`으로 선형 히스토리 재구성 가능
3. 브랜치 없이 작업: main 브랜치만으로 모든 작업 수행 가능

### 3-3. 주의사항
1. Force push: `-f` 옵션은 기존 히스토리를 덮어쓰므로 신중히 사용해야 함
2. 커밋 해시 변경: `cherry-pick`으로 새 커밋이 생성되어 해시가 변경됨

### 3-4. 결론
본 실습을 통해 브랜치를 생성하지 않고 main 브랜치만 사용하여 merge 커밋을 생성하고, 이후 선형 히스토리로 변환하는 과정을 성공적으로 수행했습니다. 이를 통해 Git의 merge 메커니즘과 히스토리 재구성 방법에 대한 이해를 높일 수 있었습니다.
