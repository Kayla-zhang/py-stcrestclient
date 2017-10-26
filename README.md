# Python STC ReST API Client: stcrestclient

The stcrestclient package provides the stchttp.py ReST API wrapper module.  This allows simple function calls, nearly identical to those provided by StcPython.py, to be used to access TestCenter server sessions via the STC ReST API.

The stcrestclient package also includes the command-line shell `tccsh` that lets you work interactively with remote STC sessions via the ReST API.

Basic ReST functionality is provided by the resthttp module, and may be used for writing ReST clients independent of STC.  This module is built on top of the [Requests](http://docs.python-requests.org/) package.

All code works with Python2.7 and Python3.x.

## Quick Start
- Get Python pip if not already installed (Download https://bootstrap.pypa.io/get-pip.py):

   `python get-pip.py`

- Install latest stcrestclient:

   `pip install -U stcrestclient`

- Write Python code to talk with TestCenter server:

   `python`
   ```python
   >>> from stcrestclient import stchttp
   >>> stc = stchttp.StcHttp('stcserver.somewhere.com')
   >>> sid = stc.new_session('JoeUser', 'ExampleTest')
   >>> stc.system_info()
   ```

- Interact with TestCenter server:

   `tccsh`

- Get information about a TestCenter server (and check if it is running):

   `stcinfo server_addr`

- Install [client adapter](https://github.com/Spirent/py-stcrestclient#automation-client-rest-api-adapter) for Python automation scripts to use ReST API, without any code change:

   ```
   python -m stcrestclient.adapt
   export STC_REST_API=1
   ```

- Display API documentation:

   `pydoc stcrestclient.stchttp`

## Project Links

- Project page: <https://github.com/Spirent/py-stcrestclient>
- Package download: <http://pypi.python.org/pypi/stcrestclient>
- Documentation: See *Spirent TestCenter Automation Programmer's Reference*
- License: <http://www.opensource.org/licenses/mit-license.php>
- Automation to ReST API: [Quick Reference](https://github.com/Spirent/py-stcrestclient/blob/master/README.md#automation-api-to-rest-api-quick-reference)

## Installation

### Install Using pip

Make sure Python pip is installed on you system.  If you are using virtualenv, then pip is already installed into environments created by virtualenv, and using sudo is not needed.  If you do not have pip installed, download this file:
<https://bootstrap.pypa.io/get-pip.py> and run `python get-pip.py`.  If installing into your system Python, you will need sufficient privileges for this, as wall as for the commands below. 

To install or upgrade to the latest, use pip to install from pypi:

    pip install -U stcrestclient

Or, install from the repository archive URL:

    pip install -U https://github.com/Spirent/py-stcrestclient/archive/master.zip

### Show information about stcrestclient:

If you want to check if the stcrestclient package is installed and see information about the installed package use the following command:

    pip show stcrestclient

### Install From Source

The stcrestclient package is installed from source using distutils in the usual way.  Download the [source distribution](https://github.com/Spirent/py-stcrestclient/archive/master.zip) first.  Unzip the zip archive and run the setup.py script to install the package site-wide.  Here are to commands to do that:

    wget https://github.com/Spirent/py-stcrestclient/archive/master.zip py-stcrestclient.zip
    unzip py-stcrestclient.zip
    cd py-stcrestclient-*
    sudo python setup.py install
    
You can also clone the [repository](https://github.com/Spirent/py-stcrestclient) from GitHub.  Instructions for this not included here.

## Using the stchttp module

To use the STC ReST client library, import the `stchttp.py` module, create a new session or join an existing session, and then call the STC API automation functions provided by the module.

```python
from stcrestclient import stchttp
stc = stchttp.StcHttp('stcserver.somewhere.com')

# Create and join new session
sid = stc.new_session('JoeUser', 'ExampleTest')

# Get system information
stc.system_info()

# Create a Project
project = stc.create('project')

# Create Port under project
port_handle = stc.create('port', project)

# Connect to a chassis
stc.connect('172.16.23.54')

# Configure port location (params as kwargs or dictionary)
stc.config(port_handle, location='//172.16.23.54/1/1')
stc.config(port_handle, {'location': "//172.16.23.54/1/1"})

# Create StreamBlock under Port
sb_handle = stc.create('streamBlock', port_handle)

# Apply config
stc.apply()

# Run STAK command to archive log files
stc.perform('spirent.core.ArchiveDiagnosticLogsCommand')

# Wait for sequencer to finish
stc.wait_until_complete(timeout=30)

# Write a message to the log file
stc.log('INFO', 'Done with my test')

# Get a list of available files
files_list = stc.files()

# Download and save the diagnostics.tgz file
name, size = stc.download('diagnostics.tgz')

# ...Or, download all files
name_size_dict = stc.download_all()

# Upload config file
stc.upload(config.xml')

# Load STC config from file (params as kwargs or dictionary).
stc.perform('LoadFromXml', filename='config.xml')
stc.perform('LoadFromXml', {'filename': 'config.xml'})

# Detach from and delete the session
stc.end_session(end_tcsession=True)
```

For example usage, look in the [examples](https://github.com/Spirent/py-stcrestclient/tree/master/examples) directory for Python code examples.  The examples, like the client lib, will run with either Python2.7 or Python3.x.

## Using the ReST API Command line Shell: tccsh

This is an interactive command shell that provides Session Manager and Automation API functionality using a command line interface.  This command accesses a TestCenter Server over its HTTP interface, so no local BLL installation is needed.  This utility is primarily useful for testing and debugging the ReST API.  It should run on any platform with Python 2.7 or 3.x.

To start the shell, use the following command:
```
tccsh
```
or
```
python -m stcrestclient.tccsh
```

You will be prompted for the server address to connect to.  You can also supply the server address on the command line: `tccsh 10.8.232.105`  To see command line options: `tccsh --help`

    usage: tccsh server [options]
	       python -m stcrestclient.tccsh server [options]

    Command shell that provides STC Automation API functionality using an
    interactive command line interface, or command file.

    positional arguments:
      server                Address of TestCenter server to connect to.

    optional arguments:
      -h, --help
          show this help message and exit

      -c COMMAND_STRING
          Command string. Multiple commands are separated by ";" (semicolon).

      --debug, -d
          Enable debug output.

      --file FILE_PATH, -f FILE_PATH
          Read commands from the specified file.

      --port PORT, -p PORT
          Server TCP port to connect to (default 80).

Notice that commands can be entered from a file (--file) or from a command string (-c), as an alternative to the normal interactive mode.

### Interactive tccsh

Once started and connected to a server, you will see a prompt that contains the address of the server you are connected to.

Type help to see help info on the available commands.  This displays the help menu:

	Documented commands (type help <topic>):
	========================================
	chassis       end           recording_off   stc_disconnect
	chassis_info  exit          recording_on    stc_disconnectall
	connections   files         server          stc_get
	debug_off     help          stc_apply       stc_help
	debug_on      info          stc_config      stc_log
	delete        is_connected  stc_connect     stc_perform
	delete_all    join          stc_connectall  system_info
	download      ls            stc_create      upload
	download_all  new           stc_delete      wait_until_complete

To see help for a command, type "help" followed by the command name.  For example, type `help ls` to see help on the `ls` command.

Use `Tab` for command auto-completion. Command auto-completion may not work on Windows.

The interactive shell has a recording feature that is enabled by the "recording_on" command.  This records commands, entered interactively, to a file that can be played back later through tccsh.  Executing the recorded commands is done by running tccsh and specifying the file containing the recorded commands, using the --file command line option.

### Command line tccsh

The tccsh shell can also be use on the command line, by specifying a file containing tccsh commands, or by providing a command string.

A file is provided using the "--file" command line option.  A command file contains commands that are executed in order, and each command is on a separate line.  Comment lines, which are lines starting with "#", and blank lines are ignored.

A command string is provided using the "-c" command line option.  A number of commands can be specified, each separated by a ";" semicolon.  For example:

   `tccsh server -c 'new jdoe test1; stc_create project; stc_create port project1'`

The above will create a new test session, create a project, and create a port object under the project.  The output looks like this:

```
Created and joined session: test1 - jdoe
created: project1
created: port1
```

## Automation Client ReST API Adapter

This package includes a ReST API client adapter module.  The ReST API client adapter is functionally identical to the legacy StcPython.py module, except that it communicates with the STC ReST API.  Without requiring any code changes, this allows STC automation scripts to communicate over ReST, and not need a local STC installation.  This ReST adapter enables ReST API access if the environment variable STC_REST_API is set to a non-empty value.  If STC_REST_API is not set, then the non-rest STC client module is loaded if there is a local STC installation.

### Installing Client Adapter module

To enable existing Python automation scripts to use the STC ReST API, run the adapt script:

```
python -m stcrestclient.adapt
```

This installs a new StcPython.py client module, that can load the ReST API adapter or the legacy client module. If there is an existing legacy StcPython.py, as when run from in the STC installation directory, StcPython.py is renamed and will be used if STC_REST_API environment vazriable is not set.  By setting or un-setting STC_REST_API, the same client scripts can choose between using a client STC instance or STC ReST API to communicate with test sessions served by TestCenter server.

Running the adapt script, to install the new StcPython.py module, and then setting STC_REST_API=1, allows existing automation scripts to run on a system where there is no STC installation.  

Nothing about the automation clients needs to change, including connecting to a TestCenter server session.

### Existing STC Sessions

If a session, identified by the specified user and session name, already exists then the adapter will raise an exception with a message stating this.  If this is not the desired behavior, then this can be changed by setting the value of the `EXISTING_SESSION` environment variable, or passing the `existing_session` argument into the `new_session()` function.  The value of `existing_session` determines the behavior, and the value may be one of the following: `kill`, `join`

- `kill`: Terminate the existing session, and create a new one.
- `join`: Continue using the existing session.

Any other value results in the default behavior, which is to raise an exception if the specified session already exists, and create a new session otherwise.

### Environment Variables

- `STC_SERVER_ADDRESS` specifies the STC server (Lab Server) addres.
- `STC_SESSION_NAME` specifies the name label part of session ID.
- `EXISTING_SESSION` specifies the behavior when the specified session already exists. Recognized values: "kill", "join"

## TestCenter system information.

The stcrestclient package includes a module, `systeminfo`, to retrieve STC and API information from a system running a TestCenter server. This module is provided as a convenient command line tool to get information about a TestCenter server.

To get information about a TestCenter server use the following command:

`stcinfo server_addr` or `python -m stcrestclient.systeminfo server_addr`

## Ending a Session

A session can be ended in three ways, depending on the value of the end_tcsession parameter of the StcHttp.end_session() method:

1. end_tcsession=None: Stop using session locally, do not contact the server.
1. end_tcsession=False: End client controller, but leave test session on server.
1. end_tcsession=True: End client controller and terminate test session (default).

Specifying end_tcsession=False is useful to do before attaching an STC GUI or legacy automation script, to prevent having multiple controllers that may interfere with each other. This requires that the server is running version 2.1.5 or later of the STC ReST API.

When using the interactive shell, tccsh, the equivalent commands for the above items are:

1. Invoke `join` with no argument
1. Invoke `end` and specify "no"
1. Invoke `end` and specify "yes"

## Requirements

- [Python2.7 or Python3.x](https://www.python.org/downloads/)

## Automation API to ReST API Quick Reference

| Automation API           | ReST API Equivalent                                    |
| ------------------------ | :----------------------------------------------------- |
| ::stc::apply	           | PUT http://<i></i>host.domain/stcapi/apply                    |
| ::stc::config	           | PUT http://<i></i>host.domain/stcapi/objects/{object}         |
| ::stc::connect           | PUT http://<i></i>host.domain/stcapi/connections/{chassis}    |
| ::stc::create            | POST http://<i></i>host.domain/stcapi/objects/                |
| ::stc::delete            | DELETE http://<i></i>host.domain/stcapi/objects/{handle}      |
| ::stc::disconnect        | DELETE http://<i></i>host.domain/stcapi/connections/{chassis} |
| ::stc::get               | GET http://<i></i>host.domain/stcapi/objects/{object}         |
| ::stc::help              | GET http://<i></i>host.domain/stcapi/help/{help_subject}      |
| ::stc::help list         | GET http://<i></i>host.domain/stcapi/help/list?{search_info}  |
| ::stc::log               | PUT http://<i></i>host.domain/stcapi/system/log/              |
| ::stc::perform           | PUT http://<i></i>host.domain/stcapi/perform/{command_name}   |
| ::stc::release           | Supported using perform                                |
| ::stc::reserve           | Supported using perform                                |
| ::stc::sleep             | NOT SUPPORTED -- client must implement                 |
| ::stc::subscribe         | Supported using perform                                |
| ::stc::unsubscribe       | Supported using perform                                |
| ::stc::waitUntilComplete | Implemented in client by polling sequencer state              |

Note: The STC ReST API supports additional methods, not specified in this table, that perform common STC automation tasks.  For example, the REST API provides methods for connecting or disconnecting multiple or all chassis using a POST request.
