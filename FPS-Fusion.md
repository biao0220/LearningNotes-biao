# Package Manager下载

com.unity.nuget.mono-cecil@1.10.2



# 项目设置

Edit > Project Settings > Editor > Asset Serialization > Mode

# 导入Fusion SDK

导入那个官网下的SDK（unitypackage文件）

# 创建应用程序ID 

0ad9d03a-873a-45f8-9fe9-4db9a21a9e0d

# 创建新物体BasicSpawner

添加组件：Network Runner

新建脚本：BasicSpawner，挂上



```
using UnityEngine;
using Fusion;
using Fusion.Sockets;
using System.Collections.Generic;
using System;

public class BasicSpawner : MonoBehaviour, INetworkRunnerCallbacks
{

    public void OnPlayerJoined(NetworkRunner runner, PlayerRef player)
    {
        
    }

    public void OnPlayerLeft(NetworkRunner runner, PlayerRef player)
    {
        
    }
    public void OnInput(NetworkRunner runner, NetworkInput input)
    {

    }
    public void OnConnectedToServer(NetworkRunner runner)
    {
        
    }

    public void OnConnectFailed(NetworkRunner runner, NetAddress remoteAddress, NetConnectFailedReason reason)
    {
        
    }

    public void OnConnectRequest(NetworkRunner runner, NetworkRunnerCallbackArgs.ConnectRequest request, byte[] token)
    {
        
    }

    public void OnCustomAuthenticationResponse(NetworkRunner runner, Dictionary<string, object> data)
    {
        
    }

    public void OnDisconnectedFromServer(NetworkRunner runner)
    {
        
    }

    public void OnHostMigration(NetworkRunner runner, HostMigrationToken hostMigrationToken)
    {
        
    }

    public void OnInputMissing(NetworkRunner runner, PlayerRef player, NetworkInput input)
    {
        
    }

    public void OnReliableDataReceived(NetworkRunner runner, PlayerRef player, ArraySegment<byte> data)
    {
        
    }

    public void OnSceneLoadDone(NetworkRunner runner)
    {
        
    }

    public void OnSceneLoadStart(NetworkRunner runner)
    {
        
    }

    public void OnSessionListUpdated(NetworkRunner runner, List<SessionInfo> sessionList)
    {
        
    }

    public void OnShutdown(NetworkRunner runner, ShutdownReason shutdownReason)
    {
        
    }

    public void OnUserSimulationMessage(NetworkRunner runner, SimulationMessagePtr message)
    {
        
    }
}
```

# 创建新物体NetworkPlayer

添加组件：Network Object

## 在它下面新建空物体：Visuals

在Visuals下新建3D物体：Capsule ：

​                                              Cube    ：Eyes



------

物体NetworkPlayer保存到文件夹Prefabs

删除物体NetworkPlayer

# 编写脚本BasicSpawner



```
using UnityEngine;
using Fusion;
using Fusion.Sockets;
using System.Collections.Generic;
using System;
using UnityEngine.SceneManagement;

public class BasicSpawner : MonoBehaviour, INetworkRunnerCallbacks
{
    [SerializeField]
    private NetworkRunner _networkRunner = null;

    [SerializeField]
    private NetworkPrefabRef _playerPrefab;

    private Dictionary<PlayerRef, NetworkObject> _playerList = new Dictionary<PlayerRef, NetworkObject>();

    private void Start()
    {
        StartGame();
    }

    private async void StartGame()
    {
        _networkRunner.ProvideInput = true;

        await _networkRunner.StartGame(new StartGameArgs()
        {
            GameMode     = GameMode.AutoHostOrClient,
            SessionName  = "FPS Game Room",
            Scene        = SceneManager.GetActiveScene().buildIndex,
            SceneManager = gameObject.AddComponent<NetworkSceneManagerDefault>()
        });
    }

    public void OnPlayerJoined(NetworkRunner runner, PlayerRef player)
    {
        Vector3 spawnPoint = new Vector3(0, 2, 0);
        NetworkObject networkPlayerObject = runner.Spawn(_playerPrefab, spawnPoint, Quaternion.identity, player);

        _playerList.Add(player, networkPlayerObject);

    }

    public void OnPlayerLeft(NetworkRunner runner, PlayerRef player)
    {
        if(_playerList.TryGetValue(player,out var networkObject))
        {
            runner.Despawn(networkObject);
            _playerList.Remove(player);
        }
    }
    public void OnInput(NetworkRunner runner, NetworkInput input)
    {

    }
    public void OnConnectedToServer(NetworkRunner runner)
    {
        
    }

    public void OnConnectFailed(NetworkRunner runner, NetAddress remoteAddress, NetConnectFailedReason reason)
    {
        
    }

    public void OnConnectRequest(NetworkRunner runner, NetworkRunnerCallbackArgs.ConnectRequest request, byte[] token)
    {
        
    }

    public void OnCustomAuthenticationResponse(NetworkRunner runner, Dictionary<string, object> data)
    {
        
    }

    public void OnDisconnectedFromServer(NetworkRunner runner)
    {
        
    }

    public void OnHostMigration(NetworkRunner runner, HostMigrationToken hostMigrationToken)
    {
        
    }

    public void OnInputMissing(NetworkRunner runner, PlayerRef player, NetworkInput input)
    {
        
    }

    public void OnReliableDataReceived(NetworkRunner runner, PlayerRef player, ArraySegment<byte> data)
    {
        
    }

    public void OnSceneLoadDone(NetworkRunner runner)
    {
        
    }

    public void OnSceneLoadStart(NetworkRunner runner)
    {
        
    }

    public void OnSessionListUpdated(NetworkRunner runner, List<SessionInfo> sessionList)
    {
        
    }

    public void OnShutdown(NetworkRunner runner, ShutdownReason shutdownReason)
    {
        
    }

    public void OnUserSimulationMessage(NetworkRunner runner, SimulationMessagePtr message)
    {
        
    }
}
```

将Network Runner组件拖入属性Network Runner

将预制体Network Player组件拖入属性Player Prefab



# 新建脚本InputData

## InputData

```
using Fusion;
using UnityEngine;

public enum InputButton
{
    Jump
}
public struct InputData : INetworkInput
{
    public NetworkButtons Button;
    public Vector2 MoveInput;
    public Angle Pitch;
    public Angle Yaw;
}

```



## BasicSpawner

```
using System.Collections.Generic;
using UnityEngine;
using Fusion;
using Fusion.Sockets;
using System;
using UnityEngine.SceneManagement;

public class BasicSpawner : MonoBehaviour, INetworkRunnerCallbacks
{
    [SerializeField]
    private float _mouseSensitivity = 10f;

    [SerializeField]
    private NetworkRunner _networkRunner = null;

    [SerializeField]
    private NetworkPrefabRef _playerPrefab;

    private Dictionary<PlayerRef, NetworkObject> _playerList = new Dictionary<PlayerRef, NetworkObject>();

    private void Start()
    {
        StartGame();
    }

    private async void StartGame()
    {
        _networkRunner.ProvideInput = true;

        await _networkRunner.StartGame(new StartGameArgs()
        {
            GameMode     = GameMode.AutoHostOrClient,
            SessionName  = "FPS Game Room",
            Scene        = SceneManager.GetActiveScene().buildIndex,
            SceneManager = gameObject.AddComponent<NetworkSceneManagerDefault>()
        });
    }

    public void OnPlayerJoined(NetworkRunner runner, PlayerRef player)
    {
        Vector3 spawnPoint = new Vector3(0, 2, 0);
        NetworkObject networkPlayerObject = runner.Spawn(_playerPrefab, spawnPoint, Quaternion.identity, player);

        _playerList.Add(player, networkPlayerObject);

    }

    public void OnPlayerLeft(NetworkRunner runner, PlayerRef player)
    {
        if(_playerList.TryGetValue(player,out var networkObject))
        {
            runner.Despawn(networkObject);
            _playerList.Remove(player);
        }
    }
    public void OnInput(NetworkRunner runner, NetworkInput input)
    {
        var inputData = new InputData();

        if (Input.GetKey(KeyCode.W)) { inputData.MoveInput += Vector2.up;    }
        if (Input.GetKey(KeyCode.S)) { inputData.MoveInput += Vector2.down;  }
        if (Input.GetKey(KeyCode.A)) { inputData.MoveInput += Vector2.left;  }
        if (Input.GetKey(KeyCode.D)) { inputData.MoveInput += Vector2.right; }

        
        inputData.Button.Set(InputButton.Jump, Input.GetKey(KeyCode.Space));

        inputData.Pitch = Input.GetAxis("Mouse Y") * _mouseSensitivity;
        inputData.Yaw = Input.GetAxis("Mouse X") * _mouseSensitivity * (-1);

        inputData.Button.Set(InputButton.Jump, Input.GetKey(KeyCode.Space));

        input.Set(inputData);



    }
   

    public void OnConnectFailed(NetworkRunner runner, NetAddress remoteAddress, NetConnectFailedReason reason)
    {
        
    }

    public void OnConnectRequest(NetworkRunner runner, NetworkRunnerCallbackArgs.ConnectRequest request, byte[] token)
    {
        
    }

    public void OnCustomAuthenticationResponse(NetworkRunner runner, Dictionary<string, object> data)
    {
        
    }

    public void OnDisconnectedFromServer(NetworkRunner runner)
    {
        
    }

    public void OnHostMigration(NetworkRunner runner, HostMigrationToken hostMigrationToken)
    {
        
    }

    public void OnInputMissing(NetworkRunner runner, PlayerRef player, NetworkInput input)
    {
        
    }

    public void OnReliableDataReceived(NetworkRunner runner, PlayerRef player, ArraySegment<byte> data)
    {
        
    }

    public void OnSceneLoadDone(NetworkRunner runner)
    {
        
    }

    public void OnSceneLoadStart(NetworkRunner runner)
    {
        
    }

    public void OnSessionListUpdated(NetworkRunner runner, List<SessionInfo> sessionList)
    {
        
    }

    public void OnShutdown(NetworkRunner runner, ShutdownReason shutdownReason)
    {
        
    }

    public void OnUserSimulationMessage(NetworkRunner runner, SimulationMessagePtr message)
    {
        
    }

    public void OnConnectedToServer(NetworkRunner runner)
    {
        
    }
}
```

