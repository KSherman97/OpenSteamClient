OpenSteamClient
===============

An open source C++ library to interface with the Steam network.

Building
--------

To build, first compile the protobufs with your platform's GenerateProtobufs script. Then use the GenerateProjects script to generate Visual Studio/make project files.

Building on Linux
-----------------

On Linux, you need to following packages to build OpenSteamClient:

```
libc6-i386 (if on amd64)
libcrypto++-dev
libprotobuf-dev
protobuf-compiler
```

Example Code
------------

Here's how you connect to the Steam network with OpenSteamClient and set your friend status to online.

```cpp
Sc::SteamClient client;
Sc::SteamUser user(client);
Sc::SteamFriends friends(client);

client.OnConnect->Add([&](Sc::ConnectEvent ev)
{
    if(ev.result == Sc::EResult_OK)
	{
		Sc::LoginDetails details;
		details.username = "";
		details.password = "";
		user.LogIn(details);
	}
	else
	{
		// Couldn't connect.
	}
});

client.OnDisconnect->Add([&](Sc::DisconnectEvent ev)
{
	// Disconnected.
});

user.OnLoggedIn->Add([&](Sc::LoggedInEvent ev)
{
	if(ev.result == Sc::EResult_OK)
	{
		friends.SetPersonaName("dajoh");
		friends.SetPersonaState(Sc::EPersonaState_Online);
	}
	else
	{
		// Couldn't log in.
	}
});

// Start connecting.
client.Connect();

while(client.IsConnected())
{
	// -1 is the timeout in ms for waiting for packets.
	// -1 means infinite.
	client.Run(-1);
}
```

Make sure to catch any exceptions thrown by SteamClient::Run(), if an exception is thrown you have to recreate the SteamClient object (and SteamFriends, etc) and connect again.

Special Thanks
--------------

Special thanks to everybody who contributed to SteamKit2, without it this project would never have been created.
