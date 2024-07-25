using UnityEngine;
using UnityEngine.Networking;

public class NetworkManager : MonoBehaviour
{
    private const int MAX_CONNECTION = 100;

    private int port = 5701;

    private int hostId;
    private int reliableChannel;
    private int unreliableChannel;

    private bool isStarted = false;
    private byte error;

    void Start()
    {
        NetworkTransport.Init();
        ConnectionConfig config = new ConnectionConfig();

        reliableChannel = config.AddChannel(QosType.Reliable);
        unreliableChannel = config.AddChannel(QosType.Unreliable);

        HostTopology topology = new HostTopology(config, MAX_CONNECTION);

        hostId = NetworkTransport.AddHost(topology, port, null);
        isStarted = true;
    }

    void Update()
    {
        if (!isStarted)
            return;

        int recHostId;
        int connectionId;
        int channelId;
        byte[] recBuffer = new byte[1024];
        int bufferSize = 1024;
        int dataSize;
        byte error;

        NetworkEventType recData = NetworkTransport.Receive(out recHostId, out connectionId, out channelId, recBuffer, bufferSize, out dataSize, out error);

        switch (recData)
        {
            case NetworkEventType.Nothing:
                break;
            case NetworkEventType.ConnectEvent:
                Debug.Log("Player " + connectionId + " has connected");
                break;
            case NetworkEventType.DataEvent:
                string msg = System.Text.Encoding.Unicode.GetString(recBuffer, 0, dataSize);
                Debug.Log("Receiving from player " + connectionId + ": " + msg);
                break;
            case NetworkEventType.DisconnectEvent:
                Debug.Log("Player " + connectionId + " has disconnected");
                break;
        }
    }

    public void SendMessage(string message)
    {
        byte[] msg = System.Text.Encoding.Unicode.GetBytes(message);
        NetworkTransport.Send(hostId, connectionId, reliableChannel, msg, message.Length * sizeof(char), out error);
    }
}
