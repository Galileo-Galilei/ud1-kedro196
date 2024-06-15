# Kedro universal deployment 1 with kedro==0.19.6

## Goal of the project

This project attempts to implement what is described in [Kedro Universal Deployment 1](https://github.com/kedro-org/kedro/issues/770) using ``kedro==0.19.6``. The goal is to help understanding how things have changed since the issue was written (``kedro==0.17)``. 

## How to use

This project is generated the ``spaceflights-pandas`` starter (``kedro new -s spaceflights-pandas``). Install the dependencies with:

```bash 
conda create -n kedro196-ud1  python=3.10 -y
conda activate kedro196-ud1 
pip install -r requirements.txt
```

## Overview of the changes

### Replace conf/base by src/conf_app

The main goal of this change is to package within the python code some "default" configuration which is more related to applicative configuration rather than extenal ones. See the issue for more details on the motivation. 

The changed made are the following : 
- Create ``src/conf_app`` folder 
- Copy the content of ``conf/base`` in ``src/conf_app``
- Delete ``conf/base`` folder
- Update ``settings.py``: 

```python
# default conf is at the root
CONF_SOURCE = "."

# Keyword arguments to pass to the `CONFIG_LOADER_CLASS` constructor.
CONFIG_LOADER_ARGS = {
    "base_env": "src/conf_app",
    "default_run_env": "conf/local",
}
```

**Advantages**:
- Very easy to configure in ``settings.py``

**Drawbacks**: 
- (nitpick) You need to type ``kedro run -e conf/my_env`` instead of ``kedro run -e my_env``
- (major) The may default is that we cannot merge 2 configuration which are not inside the same folder (here we assume that they are subfolders of the root folder). This prevents deploying configuration by passing ``conf_source`` [through the CLI with ``kedro run --conf-source=<path-to-new-conf-folder>``](https://docs.kedro.org/en/stable/configuration/configuration_basics.html#how-to-change-the-configuration-source-folder-at-runtime) because th python code and the other conf lives in two different directories. This would really be a blocker for deployment, xcept if the only changes you make come from ``runtime_params`` 


### Exposing configuration with runtime_params

**Advantages**:
- Works out of the box, since ``OmegaConfigLoader`` is the default and the ``runtime_params`` is a native resolver. 

**Drawbacks**: 
- no log if invalid param (which does not exist)
- no log to indicate that the param is overriden when it works
- no CLI command to see which runtime_params are available ``kedro config list_runtime_params -p <pipeline>``
- not possible to create this LCI command locally because of https://github.com/kedro-org/kedro/issues/2973


### Exposing credentials

- Possible to create a custom resolver and register it. 
- Why not make ``oc.env`` registered by default? List issues because this has been discussed a lot. 