# Gemini Prompt v1 — Sprint 1: Каркас + игровой экран

## Контекст проекта

Android-игра **Water Measure** — головоломка с переливанием воды между сосудами.  
**Sprint 1** — создание работающего экрана игры с отрисовкой сосудов, тапами, переливом и проверкой победы.

**Stack:** Java 17, Android Studio, Gradle (Kotlin DSL).  
**Package root:** `com.watermeasure.game`  
**Архитектура:** пока без MVVM, без Room, без DataStore. Только `model` + `view` (Activity + Custom View).  
**UI:** рисование кодом на `Canvas` (без XML-разметки для сосудов).  
**Источник данных:** хардкод 3 уровней в коде (позже заменим на JSON).

## Как выдавать результат

- Один файл за раз. После каждого файла — 1–2 предложения пояснения.
- Жди от пользователя "дальше" перед следующим файлом.
- Полный код — без `// TODO`, без `...`, без плейсхолдеров. Должно компилироваться сразу.
- Пути к файлам указывай явно.
- Комментарии в коде — на English (стандарт для Java).

## Список файлов (по порядку)

### Файл 1: `model/Vessel.java`

```java
package com.watermeasure.game.model;

public class Vessel {
    private final int index;
    private final int capacity;
    private int currentVolume;
    private final int initialVolume;
    private final boolean isSource;

    // constructor + getters + setCurrentVolume с валидацией
}
```

**Requirements:**
- `capacity > 0`
- `0 <= currentVolume <= capacity`
- `isSource` — просто флаг. Источник не имеет специальной логики в модели.
- Validation: throw `IllegalArgumentException` при нарушении инвариантов.
- `getIndex()`, `getCapacity()`, `getCurrentVolume()`, `getInitialVolume()`, `isSource()`, `setCurrentVolume(int)`.

### Файл 2: `model/Level.java`

```java
package com.watermeasure.game.model;

import java.util.List;

public class Level {
    private final int id;
    private final List<Vessel> vessels;
    private final int targetVolume;

    // constructor + getters
}
```

### Файл 3: `logic/TransferWater.java`

```java
package com.watermeasure.game.logic;

import com.watermeasure.game.model.Vessel;

public class TransferWater {
    public static boolean transfer(Vessel src, Vessel dst) {
        // algorithm
    }
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

### Файл 4: `logic/CheckWin.java`

```java
package com.watermeasure.game.logic;

import com.watermeasure.game.model.Level;
import com.watermeasure.game.model.Vessel;

public class CheckWin {
    public static boolean check(Level level) {
        // return true if any vessel with isSource == false has currentVolume == level.getTargetVolume()
    }
}
```

### Файл 5: `view/GameView.java` (Custom View)

```java
package com.watermeasure.game.view;

import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.view.MotionEvent;
import android.view.View;

import com.watermeasure.game.model.Level;
import com.watermeasure.game.model.Vessel;
import com.watermeasure.game.logic.TransferWater;
import com.watermeasure.game.logic.CheckWin;

public class GameView extends View {
    // fields: Level level, Vessel selectedVessel, Paint paints, coordinates
    // constructor: set up paints, load level (хардкод 3 уровней)
    // onDraw: draw 3 vessels (rectangles), water inside, highlight selected
    // onTouchEvent: handle taps (select/deselect/transfer)
}
```

**Requirements:**
- Рисует 3 сосуда в ряд (или треугольником — на твоё усмотрение, но равномерно по ширине экрана).
- Каждый сосуд — прямоугольник с закруглёнными углами (или обычный).
- Внутри — голубой прямоугольник, высота = `(currentVolume / capacity) * vesselHeight`.
- Выбранный сосуд — обводка красным/жёлтым цветом (2–3 dp).
- Обработка `onTouchEvent`:
  - Определить, по какому сосуду тапнули (по координатам).
  - Если нет выбранного → выбрать (если сосуд не пустой).
  - Если есть выбранный → попытаться перелить (`TransferWater.transfer`).
  - После перелива — снять выделение, вызвать `invalidate()`.
  - После перелива проверить `CheckWin.check(level)`. Если победа — показать попап (через слушатель или прямо в `GameView` создать `AlertDialog`).

### Файл 6: `GameActivity.java`

```java
package com.watermeasure.game;

import android.app.Activity;
import android.os.Bundle;
import android.widget.FrameLayout;
import android.widget.TextView;

import com.watermeasure.game.view.GameView;

public class GameActivity extends Activity {
    // setContentView: FrameLayout с TextView (номер уровня) + GameView
}
```

**Requirements:**
- Создать `FrameLayout` программно (без XML).
- Добавить `TextView` сверху с текстом «Уровень: 1» (или номер текущего уровня).
- Добавить `GameView` на весь экран (под TextView).
- В `onCreate` инициализировать `GameView` с уровнем 1 (или массивом из 3 уровней).

### Файл 7: `AndroidManifest.xml` (фрагмент)

Убедись, что `GameActivity` объявлена как launcher activity.

---

## Дополнительно

- Не создавай `ViewModel`, `Room`, `DataStore`, `SharedPreferences` в этом спринте.
- Не используй Compose. Только классические `View` и `Canvas`.
- Не добавляй анимации (это Спринт 3). Только мгновенная перерисовка после перелива.
- Если нужно — создай простой массив из 3 уровней прямо в `GameView` или `GameActivity` (хардкод).
