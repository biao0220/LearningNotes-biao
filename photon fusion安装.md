com.unity.nuget.mono-cecil@1.10.2

# 项目设置

 一些融合设置将保存在ScriptableObject资源中。为了使这些设置始终清晰可见，资产序列化的模式必须设置为Force Text

​    在Edit > Project Settings > Editor > Asset Serialization > Mode. 

Fusion IL Weaver生成低级网络代码并将其注入Assembly-CSharp.dll。为了实现这一目标，使用了Mono Cecil软件包。软件包可以通过Unity软件包管理器安装。 

导入路径Window > Package Manager > Click the + icon > Add package from git URL

com.unity.nuget.mono-cecil@1.10.2



# 导入Fusion SDK

项目现在可以导入Fusion SDK了。SDK以unitypackage文件的形式提供，可以使用`Assets > Import Package > Custom Package`工具。只需导航到下载SDK的位置并触发导入。

# 创建应用程序ID 

导入完成后，将弹出Fusion Hub向导。这Welcome屏幕将要求输入应用程序ID。在填写之前，需要创建一个新的应用ID。

应用程序ID是应用程序标识符，用于:

识别应用程序；
将应用程序与正确类型的服务器插件相关联-在本例中为Fusion而且，
使用应用程序连接玩家。

# 添加AppID，复制仪表板上显示的应用程序Id，并粘贴到Fusion Hub中。



# 主机模式基础使用

2.1 场景初始设置，并创建简单玩家生成逻辑
2.1.1  创建脚本BasicSpawner
1）、创建空物体NetworkRunner，

2）、创建脚本BasicSpawner，挂载到空物体上

3）、打开脚本，将INetworkRunnerCallbacks接口添加到BasicSpawner类，以及所有所需方法的存根

```
INetworkRunnerCallbacks 接口的作用主要是为网络库或框架提供一个方式来通知应用程序（在这种情况下是Unity游戏或应用）关于网络事件。当网络运行器（NetworkRunner）检测到某些网络相关的事件时，它会调用实现了 INetworkRunnerCallbacks 接口的类的相应方法。

具体来说，INetworkRunnerCallbacks 接口定义了一系列回调函数，这些函数会在以下情况下被调用：

有玩家加入或离开游戏时（OnPlayerJoined, OnPlayerLeft）。
网络连接状态发生变化时（OnConnectedToServer, OnDisconnectedFromServer）。
收到玩家输入或未收到玩家输入时（OnInput, OnInputMissing）。
网络运行器关闭时（OnShutdown）。
以及其他各种网络相关的事件，如场景加载、对象进入或离开AOI（Area of Interest，感兴趣区域）、接收到可靠数据等。
这些回调函数为开发者提供了在网络事件发生时执行特定逻辑的能力。例如，当玩家加入游戏时，你可能想要在游戏世界中为该玩家创建一个新的游戏对象。当网络连接断开时，你可能想要通知玩家并清理与该连接相关的所有资源。

总之，INetworkRunnerCallbacks 接口是网络库和应用程序之间的桥梁，它使得网络库能够通知应用程序关于网络状态的变化，并允许应用程序根据这些变化执行相应的逻辑。
```



BasicSpawner 当前代码

```c#
using UnityEngine;
using UnityEngine.SceneManagement;
using Fusion;
using Fusion.Sockets;
using System.Collections.Generic;
using System;

public class BasicSpawner : MonoBehaviour, INetworkRunnerCallbacks
{
    public void OnPlayerJoined(NetworkRunner runner, PlayerRef player) { }
    public void OnPlayerLeft(NetworkRunner runner, PlayerRef player) { }
    public void OnInput(NetworkRunner runner, NetworkInput input) { }
    public void OnInputMissing(NetworkRunner runner, PlayerRef player, NetworkInput input) { }
    public void OnShutdown(NetworkRunner runner, ShutdownReason shutdownReason) { }
    public void OnConnectedToServer(NetworkRunner runner) { }
    public void OnDisconnectedFromServer(NetworkRunner runner, NetDisconnectReason reason) { }
    public void OnConnectRequest(NetworkRunner runner, NetworkRunnerCallbackArgs.ConnectRequest request, byte[] token) { }
    public void OnConnectFailed(NetworkRunner runner, NetAddress remoteAddress, NetConnectFailedReason reason) { }
    public void OnUserSimulationMessage(NetworkRunner runner, SimulationMessagePtr message) { }
    public void OnSessionListUpdated(NetworkRunner runner, List<SessionInfo> sessionList) { }
    public void OnCustomAuthenticationResponse(NetworkRunner runner, Dictionary<string, object> data) { }
    public void OnHostMigration(NetworkRunner runner, HostMigrationToken hostMigrationToken) { }
    public void OnSceneLoadDone(NetworkRunner runner) { }
    public void OnSceneLoadStart(NetworkRunner runner) { }
    public void OnObjectExitAOI(NetworkRunner runner, NetworkObject obj, PlayerRef player) { }
    public void OnObjectEnterAOI(NetworkRunner runner, NetworkObject obj, PlayerRef player) { }
    public void OnReliableDataReceived(NetworkRunner runner, PlayerRef player, ReliableKey key, ArraySegment<byte> data) { }
    public void OnReliableDataProgress(NetworkRunner runner, PlayerRef player, ReliableKey key, float progress) { }
    public void OnDisconnectedFromServer(NetworkRunner runner) { }
    public void OnReliableDataReceived(NetworkRunner runner, PlayerRef player, ArraySegment<byte> data) { }
}
```



 实现inetworkrunnercallback将允许Fusions NetworkRunner与BasicSpawner进行交互。NetworkRunner是Fusion的核心和灵魂，运行实际的网络模拟。

​    NetworkRunner将自动检测BasicSpawner实现INetworkRunnerCallbacks并调用其方法，因为它将在StartGame方法中添加到相同的游戏对象中。





这段代码是Unity C#脚本的一部分，主要涉及到网络游戏的初始化和启动。我会为你详细解释每一部分的作用：

1. **字段定义**

```csharp
[SerializeField]  
private NetworkRunner networkRunner = null;
```

这里定义了一个私有字段`networkRunner`，它的类型是`NetworkRunner`（可能是一个管理网络游戏的类）。`[SerializeField]`特性使得这个私有字段可以在Unity的Inspector窗口中显示和编辑，尽管它本身是私有的。这允许开发者在Unity编辑器中直接设置`networkRunner`的值，而不需要修改脚本代码。

1. **Start方法**

```csharp
private void Start()  
{  
    StartGame(GameMode.AutoHostOrClient);  
}
```

Start`是Unity MonoBehaviour生命周期中的一个方法，它在对象被创建并首次启用时调用。在这个方法中，调用了`StartGame`方法，并传递了一个`GameMode.AutoHostOrClient`枚举值作为参数。这通常意味着游戏将尝试自动成为主机或客户端，具体取决于网络环境和配置。

1. **StartGame方法**

```csharp
async void StartGame(GameMode node)  
{  
    networkRunner.ProvideInput = true;  
  
    await networkRunner.StartGame(new StartGameArgs()  
    {  
        GameMode = node,  
        SessionName = "Fusion Room",  
        Scene = SceneManager.GetActiveScene().buildIndex,  
        SceneManager = gameObject.AddComponent<NetworkSceneManagerDefault>()  
    });  
}
```

StartGame`是一个异步方法（使用`async`关键字），它接受一个`GameMode`类型的参数`node`。这个方法执行了以下操作：

- 设置`networkRunner`的`ProvideInput`属性为`true`。这可能意味着`networkRunner`将开始处理或发送玩家的输入。

- 调用

  ```
  networkRunner
  ```

  的

  ```
  StartGame
  ```

  方法，并传递一个

  ```
  StartGameArgs
  ```

  对象作为参数。这个对象包含了启动游戏所需的信息：

  - `GameMode`：从`StartGame`方法的参数`node`中获取，指定了游戏的模式（在这里是自动主机或客户端）。
  - `SessionName`：设置了游戏会话的名称为"Fusion Room"。
  - `Scene`：获取当前活动场景的构建索引（`buildIndex`），并将其作为启动参数。这允许网络库知道要加载哪个场景。
  - `SceneManager`：为当前游戏对象（`gameObject`）添加了一个`NetworkSceneManagerDefault`组件，并将其作为启动参数。这可能是一个管理网络场景中对象加载和卸载的组件。

使用`await`关键字意味着`StartGame`方法会等待`networkRunner.StartGame`方法的完成，而不会阻塞主线程。这对于异步操作（如网络请求）是很重要的，因为它允许Unity继续处理其他事情，直到网络操作完成。

总的来说，这段代码的主要目的是初始化并启动一个网络游戏会话，它使用`NetworkRunner`类来管理网络功能，并设置了一些启动参数，如游戏模式、会话名称、要加载的场景以及场景管理器组件。



`await`是一个关键字，用于在`async`方法内部等待一个异步操作完成。它只能用在`async`方法内部





# 创建玩家预制体

​        为了让这成为一款游戏，每个玩家都必须拥有一种提供输入的方式，并在游戏世界中拥有自己的存在感，如玩家角色。
在Unity编辑器中，
1）、创建一个名为PlayerPrefab的新空Gameobject

2）、向其中添加一个Networkobject组件。
      继续并添加NetworkCharacterController



在你提供的代码片段中，我们看到了两个方法，`OnPlayerJoined` 和 `OnPlayerLeft`，它们分别处理玩家加入和离开网络游戏的逻辑。以下是对这两个方法的详细解释：

### OnPlayerJoined 方法

这个方法在玩家加入游戏时被调用。它接收两个参数：`NetworkRunner runner` 和 `PlayerRef player`。

1. `Vector3 spawnPosition = Vector3.up * 2;` 这行代码定义了一个向量，该向量指向世界空间中的垂直上方，距离原点2个单位。这通常用于确定新玩家的初始位置。
2. `NetworkObject networkPlayerObject = runner.Spawn(playerPrefabs, spawnPosition, Quaternion.identity, player);` 这行代码使用 `NetworkRunner` 的 `Spawn` 方法来在网络中创建一个新的游戏对象（可能代表一个玩家）。这里假设 `playerPrefabs` 是一个预制体（Prefab）的集合或者一个特定预制体的引用，用于实例化新的玩家对象。新对象的初始位置是 `spawnPosition`，初始旋转是 `Quaternion.identity`（即没有旋转），并且与传入的 `player` 引用相关联。
3. `playerList.Add(player, networkPlayerObject);` 这行代码将新创建的 `NetworkObject` 与其对应的 `PlayerRef` 添加到 `playerList` 字典中，以便以后引用。

### OnPlayerLeft 方法

这个方法在玩家离开游戏时被调用，同样接收 `NetworkRunner runner` 和 `PlayerRef player` 作为参数。

1. `if(playerList.TryGetValue(player,out NetworkObject networkObject))` 这行代码尝试从 `playerList` 字典中获取与指定 `PlayerRef` 相关联的 `NetworkObject`。如果找到，`TryGetValue` 方法将返回 `true` 并将找到的 `NetworkObject` 赋值给 `networkObject` 变量。
2. `runner.Despawn(networkObject);` 如果成功地从 `playerList` 中获取了 `NetworkObject`，这行代码将调用 `NetworkRunner` 的 `Despawn` 方法来销毁或取消实例化该对象，从而将其从网络游戏中移除。
3. `playerList.Remove(player);` 最后，从 `playerList` 字典中移除与已离开玩家的 `PlayerRef` 相关联的条目。

注意：

- 代码中假设 `playerList` 是一个 `Dictionary<PlayerRef, NetworkObject>` 类型的变量，用于存储玩家引用和游戏对象之间的映射关系。
- `playerPrefabs` 应该是一个在方法外部定义并初始化的变量，它指向用于实例化新玩家的预制体。
- 代码中未显示 `NetworkRunner`、`PlayerRef` 和 `NetworkObject` 的具体实现和定义，这些通常是在你使用的网络框架或库中定义的。













————————————————

                            版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。

原文链接：https://blog.csdn.net/zhoutao2333/article/details/136912209





















————————————————

                            版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。

原文链接：https://blog.csdn.net/zhoutao2333/article/details/136912209