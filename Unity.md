# 脚本生命周期

* `Awake()` 创建游戏对象 立即执行一次（游戏物体禁用时不执行），适合初始化操作
* `OnEnable()` 每当脚本对象启用时调用
* `Start()` 创建游戏对象 脚本启用 才执行一次（脚本禁用时不执行）适合初始化操作
* `Update()` 渲染帧执行，间隔不固定，适合处理游戏逻辑
* `FixedUpdate()` 每隔固定时间执行一次（默认为0.02s）,适合对物体做移动、旋转等物理操作，不会受到渲染影响
* `LateUpdate()` 在`Update()`调用后执行，适用于跟随逻辑（与`Update()`在同一帧）

> 所有游戏物体的`Awake()`先执行一遍，之后再执行`Start()`

* `OnDisable()` 每次脚本或游戏对象禁用时调用
* `OnDestroy()` 脚本或游戏对象被销毁时调用
* `OnApplicationQuit()` 应用程序退出时调用

# Time

* `deltaTime` 一帧所用的时间（speed * Time.deltaTime），受`timeScale`影响
* `timeScale` 时间缩放，可用于减慢运动效果。`FixedUpdate()`受`timeScale`影响（`timeScale`为0时，`FixedUpdate()`不执行）
* `unscaledDeltaTime` 不受`timeScale`影响
* `realtimeSinceStartup` 实际运行时间

```C
// 倒计时
private float nextTime = 1;
private float totalTime;
private void Update()
{
    // 方法一
    if (Time.time >= nextTime)
    {
        second--;
        txtTimer.txt = string.Format("{0:d2}:{1:d2}", second / 60, second % 60);

        nextTime = Time.time + 1;
    }

    // 方法二
    totalTime += Time.deltaTime;
    if (totalTime >= 1)
    {
        second--;
        txtTimer.txt = string.Format("{0:d2}:{1:d2}", second / 60, second % 60);

        totalTime = 0;
    }

    
}

private void Start()
{
    // 方法三
    InvokeRepeating("Timer", 1, 1);     // 重复调用(方法名称，第一次执行时间，每次执行间隔)
}

private void Timer()
{
    second--;
    txtTimer.txt = string.Format("{0:d2}:{1:d2}", second / 60, second % 60);

    if (second <= 0)
    {
        CancelInvoke("Timer");
    }
}

````
