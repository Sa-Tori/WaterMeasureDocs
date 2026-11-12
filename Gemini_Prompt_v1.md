# Gemini Prompt v1 — Sprint 1: Математическое ядро Water Measure

## Контекст проекта

Android-игра **Water Measure** — головоломка с переливанием воды между сосудами.
Sprint 1 — **только математическое ядро, без UI**. Всё проверяется через JUnit 5.

- **Stack:** Java 17, Android Studio, Gradle (Kotlin DSL), JUnit 5, Gson
- **Package root:** `com.watermeasure.game`
- **Архитектура:** слои `model`, `logic`, `repository`. Без MVVM/Activity/Compose в этом спринте.

## Как выдавать результат

1. **Один файл за раз.** После каждого файла — 1–2 предложения пояснения. Жди от пользователя "дальше" перед следующим.
2. Полный код — без `// TODO`, без `...`, без плейсхолдеров. Должно компилироваться сразу.
3. Пути к файлам указывай явно.
4. Комментарии в коде — на **English** (стандарт для Java).
5. Package declarations — точные, как указано ниже.

## Список файлов (по порядку)

### Файл 1: `app/src/main/java/com/watermeasure/game/model/Vessel.java`

```java
package com.watermeasure.game.model;

public class Vessel {
    private final int index;
    private final int capacity;
    private int currentVolume;
    private final boolean isSource;
    // constructor + getters + setCurrentVolume с валидацией
}
```

**Requirements:**
- `capacity > 0`
- `0 <= currentVolume <= capacity`
- `isSource` — просто флаг. Источник не имеет специальной логики в модели.
- Validation: throw `IllegalArgumentException` при нарушении инвариантов.
- `getIndex()`, `getCapacity()`, `getCurrentVolume()`, `isSource()`, `setCurrentVolume(int)`.

### Файл 2: `.../model/Level.java`

```java
package com.watermeasure.game.model;

public class Level {
    private final int id;
    private final List<Vessel> vessels;
    private final int targetVolume;
    // constructor + getters
}
```

### Файл 3: `.../logic/TransferWater.java`

```java
package com.watermeasure.game.logic;

public class TransferWater {
    public static boolean transfer(Vessel src, Vessel dst) { ... }
}
```

**Algorithm (strict order):**
1. `if (src == dst) return false;`
2. `if (src.getCurrentVolume() == 0) return false;`
3. `if (dst.getCurrentVolume() == dst.getCapacity()) return false;`
4. `int amount = Math.min(src.getCurrentVolume(), dst.getCapacity() - dst.getCurrentVolume());`
5. `src.setCurrentVolume(src.getCurrentVolume() - amount);`
6. `dst.setCurrentVolume(dst.getCurrentVolume() + amount);`
7. `return true;`
- **No special handling for `isSource`.**

### Файл 4: `.../logic/CheckWin.java`

```java
package com.watermeasure.game.logic;

public class CheckWin {
    public static boolean check(Level level) {
        // return true if any vessel with isSource == false has currentVolume == level.getTargetVolume()
    }
}
```

### Файл 5: `app/src/main/res/raw/levels.json`

JSON schema:
```json
{
  "levels": [
    {
      "id": 1,
      "vessels": [
        {"capacity": 3, "initialVolume": 0, "isSource": false},
        {"capacity": 5, "initialVolume": 0, "isSource": false},
        {"capacity": 8, "initialVolume": 8, "isSource": false}
      ],
      "targetVolume": 4,
      "solutionSteps": [[0,0,8],[0,5,3],[3,2,3]]
    }
  ]
}
```

Для источника (`isSource: true`): `capacity` = `v1 + v2 + targetVolume + 1`. `initialVolume` = `capacity`.

**Готовые 20 уровней — вставить в `levels.json`:**

```json
{
  "levels": [
    {"id":1,"vessels":[{"capacity":3,"initialVolume":0,"isSource":false},{"capacity":5,"initialVolume":0,"isSource":false},{"capacity":8,"initialVolume":8,"isSource":false}],"targetVolume":4},
    {"id":2,"vessels":[{"capacity":3,"initialVolume":0,"isSource":false},{"capacity":5,"initialVolume":0,"isSource":false},{"capacity":10,"initialVolume":10,"isSource":true}],"targetVolume":1},
    {"id":3,"vessels":[{"capacity":7,"initialVolume":0,"isSource":false},{"capacity":5,"initialVolume":0,"isSource":false},{"capacity":19,"initialVolume":19,"isSource":true}],"targetVolume":6},
    {"id":4,"vessels":[{"capacity":2,"initialVolume":0,"isSource":false},{"capacity":3,"initialVolume":0,"isSource":false},{"capacity":5,"initialVolume":5,"isSource":false}],"targetVolume":1},
    {"id":5,"vessels":[{"capacity":5,"initialVolume":0,"isSource":false},{"capacity":7,"initialVolume":0,"isSource":false},{"capacity":8,"initialVolume":8,"isSource":false}],"targetVolume":6},
    {"id":6,"vessels":[{"capacity":3,"initialVolume":0,"isSource":false},{"capacity":4,"initialVolume":0,"isSource":false},{"capacity":10,"initialVolume":10,"isSource":true}],"targetVolume":2},
    {"id":7,"vessels":[{"capacity":3,"initialVolume":0,"isSource":false},{"capacity":4,"initialVolume":0,"isSource":false},{"capacity":7,"initialVolume":7,"isSource":false}],"targetVolume":2},
    {"id":8,"vessels":[{"capacity":3,"initialVolume":0,"isSource":false},{"capacity":5,"initialVolume":0,"isSource":false},{"capacity":8,"initialVolume":8,"isSource":false}],"targetVolume":1},
    {"id":9,"vessels":[{"capacity":2,"initialVolume":0,"isSource":false},{"capacity":5,"initialVolume":0,"isSource":false},{"capacity":9,"initialVolume":9,"isSource":true}],"targetVolume":1},
    {"id":10,"vessels":[{"capacity":2,"initialVolume":0,"isSource":false},{"capacity":5,"initialVolume":0,"isSource":false},{"capacity":12,"initialVolume":12,"isSource":true}],"targetVolume":4},
    {"id":11,"vessels":[{"capacity":4,"initialVolume":0,"isSource":false},{"capacity":5,"initialVolume":0,"isSource":false},{"capacity":13,"initialVolume":13,"isSource":true}],"targetVolume":3},
    {"id":12,"vessels":[{"capacity":1,"initialVolume":0,"isSource":false},{"capacity":7,"initialVolume":0,"isSource":false},{"capacity":8,"initialVolume":8,"isSource":false}],"targetVolume":5},
    {"id":13,"vessels":[{"capacity":3,"initialVolume":0,"isSource":false},{"capacity":5,"initialVolume":0,"isSource":false},{"capacity":13,"initialVolume":13,"isSource":true}],"targetVolume":4},
    {"id":14,"vessels":[{"capacity":5,"initialVolume":0,"isSource":false},{"capacity":8,"initialVolume":0,"isSource":false},{"capacity":20,"initialVolume":20,"isSource":true}],"targetVolume":6},
    {"id":15,"vessels":[{"capacity":4,"initialVolume":0,"isSource":false},{"capacity":5,"initialVolume":0,"isSource":false},{"capacity":12,"initialVolume":12,"isSource":true}],"targetVolume":2},
    {"id":16,"vessels":[{"capacity":5,"initialVolume":0,"isSource":false},{"capacity":8,"initialVolume":0,"isSource":false},{"capacity":21,"initialVolume":21,"isSource":true}],"targetVolume":7},
    {"id":17,"vessels":[{"capacity":4,"initialVolume":0,"isSource":false},{"capacity":7,"initialVolume":0,"isSource":false},{"capacity":14,"initialVolume":14,"isSource":true}],"targetVolume":2},
    {"id":18,"vessels":[{"capacity":3,"initialVolume":0,"isSource":false},{"capacity":5,"initialVolume":0,"isSource":false},{"capacity":14,"initialVolume":14,"isSource":true}],"targetVolume":5},
    {"id":19,"vessels":[{"capacity":5,"initialVolume":0,"isSource":false},{"capacity":6,"initialVolume":0,"isSource":false},{"capacity":15,"initialVolume":15,"isSource":true}],"targetVolume":3},
    {"id":20,"vessels":[{"capacity":6,"initialVolume":0,"isSource":false},{"capacity":7,"initialVolume":0,"isSource":false},{"capacity":18,"initialVolume":18,"isSource":true}],"targetVolume":4}
  ]
}
```

### Файл 6: `.../repository/LevelRepository.java`

```java
package com.watermeasure.game.repository;

public class LevelRepository {
    public List<Level> loadLevels(Context context) throws IOException { ... }
}
```

**Requirements:**
- Читает `R.raw.levels` через `context.getResources().openRawResource(R.raw.levels)`
- Парсит через Gson
- Возвращает `List<Level>`
- Внутренний `LevelJson` DTO для парсинга

### Файл 7: `app/src/test/java/com/watermeasure/game/WaterMeasureTest.java`

10 JUnit 5 тестов (все зелёные):
1. `transfer_ShouldPourPartially_WhenSourceHasLessThanSpace`
2. `transfer_ShouldFillCompletely_WhenSourceHasMore`
3. `transfer_ShouldReturnFalse_WhenDestinationFull`
4. `transfer_ShouldReturnFalse_WhenSourceEmpty`
5. `transfer_ShouldReturnFalse_WhenSameVessel`
6. `transfer_FromSource_WorksAsNormalVessel`
7. `transfer_ToSource_WorksAsNormalVessel`
8. `checkWin_ShouldReturnTrue_WhenTargetReached`
9. `checkWin_ShouldReturnFalse_WhenTargetNotReached`
10. `checkWin_ShouldIgnoreSourceVessel`

### Файл 8 (S1-06, опционально): `.../LevelSolvabilityTest.java`

BFS по всем 20 уровням. Состояние = массив `currentVolume` всех сосудов (включая `isSource`). Действия: `X→Y` для всех пар. Цель — `CheckWin.check(state)`.

**Requirements:**
- `@ParameterizedTest` по уровням из `levels.json`
- BFS с `Set<String>` visited
- `@Test` для каждого уровня — `assertTrue(solvable(level))`
- Limit: 100k states на уровень

## Что НЕ нужно в Sprint 1

- ❌ Activity, Fragment, Compose, XML layouts
- ❌ Room, DataStore, Navigation Component
- ❌ Анимации, звуки, Canvas, тап-обработчики
- ❌ MVVM-ViewModel
- ❌ Жизни, Undo, Локализация (это Sprint 2+)

## Definition of Done для этого промпта

- [ ] Все 8 файлов созданы в правильных путях
- [ ] `./gradlew test` проходит (10 + 20 тестов зелёные)
- [ ] Все 20 уровней решаемы по BFS
