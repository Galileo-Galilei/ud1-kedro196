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
