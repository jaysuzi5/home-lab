# dice-roll
This is the getting started example from OpenTelementry.  It is a simple Flask application to return a random roll of a dice.  The main purpose is to capture OpenTelementry logs, traces, and metrics with a low code approach.  This does require some code changes to the Python code, but very litte.  This will be using my existing OTEL collector.

See further details on the Python code:  https://github.com/jaysuzi5/dice-roll


## Project Structure

```bash
cluster/
  ├── apps/
  │   ├── dice-roll/
  │   │   ├── namespace.yaml
  │   │   ├── deployment.yaml
  │   │   ├── service.yaml
  │   │   ├── kustomization.yaml
  │   │   └── weather-kustomization.py
  │   │   
```
