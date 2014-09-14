# pushbullet-bash
* Originally authored by [Red5d](https://github.com/Red5d)
* This fork by [Sean Payne](https://github.com/zerodivide1)

## Description
This is a Bash interface to the [PushBullet API](https://www.pushbullet.com/).

It can list your available devices and push different types of data to them. It can also manipulate the pushes in your account, as well as execute commands as new pushes arrive in your account.

## Requirements
* curl
* jq (http://stedolan.github.io/jq/)
* wssh (https://github.com/progrium/wssh) (_optional_)

## Installation
1. Clone the repository:
```
git clone https://github.com/zerodivide1/pushbullet-bash
```
2. Make the Pushbullet script executable:
```
chmod u+x pushbullet-bash/pushbullet
```
3. Either add the script directory to your `$PATH` or add a symbolic link to it to your `/usr/local/bin/` directory:
```
ln -s $(pwd)/pushbullet-bash/pushbullet /usr/local/bin/pushbullet
```

## Configuration
Create a new file `$HOME/.config/pushbullet` and add the following:
```
API_KEY=YOUR_API_KEY
```
You can find your API key on your [account settings page](https://www.pushbullet.com/account).

Alternatively, you can call Pushbullet in a single line by setting the variable $CONFIG in the same call to pushbullet. For example:
```
CONFIG=/path/to/pushbulletconfig bash -c pushbullet...
```

## Usage
The script can be invoked to query the list of devices you can push to, push data to all or a specific device, retrieve pushes or stream them.
### Devices
To retrieve the list of devices:
```
$ pushbullet list
```
### Pushing
There are 5 types of data you can push:
* Note
* Links
* Addresses
* Lists (_coming soon_)
* Files (_coming soon_)

The typical pattern to push is:
```
$ pushbullet push <Device name> <type> <type args>
```

#### Notes
Notes accept a title and a body as parameters:
```
$ pushbullet push <Device name> note <title> <body>
```

#### Links
Links accept a title and URL as parameters:
```
$ pushbullet push <Device name> link <title> <url>
```

#### Addresses
Addresses accept a name and a place address (or map search query):
```
$ pushbullet push <Device name> address <name> <address>
```

### Retrieving
#### Recent pushes
You can retrieve a list of recent pushes by using the `recent` command. You can filter the returned list by optionally including a type. The command will only return the list of recent pushes since the last time the `recent` command was used without any type. Valid types are `note`, `link`, `list`, `address`, and `file`.

Example:
```
$ pushbullet recent
<Returns a JSON array of pushes: 'iden', type, title, name and file name>
```

Example with type:
```
$ pushbullet recent note
<Returns a JSON array of notes: 'iden', 'title', 'body'>
```

#### Specific push
You can retrieve a specific push by using the `get` command and including the `iden` of the push to retrieve.

Example:
```
$ pushbullet get <iden>
<Returns the raw JSON data for the push from Pushbullet>
```

### Deleting
To delete a push, use the `delete` command with the `iden` of the push to delete:
```
$ pushbullet delete <iden>
```

### Streaming
The script also allows executing a command on each new push in the account. The `iden` of the push is passed as the last argument to the command executed.

The command takes the following form:
```
$ pushbullet stream <command> <command args>
```

Example echoing the `iden` of each new push:
```
$ pushbullet stream echo
```

Example outputing the information about each new push to stdout:
```
$ pushbullet stream pushbullet get
```

If using a complex chain of commands, it is recommended that the complex logic is wrapped in a script where the `iden` of the push can be passed as an argument.

Also note that the command may be executed for the same `iden` more than once.
