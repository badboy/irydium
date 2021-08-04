---
title: Python Language Plugin
imports:
  - /components/plotlyjs.md#Plotly
---

# Python Language Plugin

Irydium supports the ideas of language plugins, defined inside the document itself.
This has many advantages, but one interesting benefit is that it lets us prototype support for interesting and/or experimental language runtimes.
In this case, we'll use this feature to run the currently in-development pyodide 0.18.0 release.

This particular plugin is always available when you're using Irydium: no need to import it explicitly.

Below is an example of a python language plugin:

```{code-cell} js
---
id: python
type: language-plugin
scripts: https://cdn.jsdelivr.net/pyodide/v0.18.0a1/full/pyodide.js
---
const pyodide = await loadPyodide({
  indexURL: "https://cdn.jsdelivr.net/pyodide/v0.18.0a1/full/",
});

return async (inputs, code) => {
  const preamble = (inputs || [])
            .map((i) => `from js import ${i}`)
            .join("\n");
	await pyodide.loadPackagesFromImports(`${preamble}${code}`);
  const result = await pyodide.runPythonAsync(`${preamble}${code}`);
  // if result has type conversion code, then use that
  if (result && result.toJs) {
    return result.toJs();
  }
  return result;
}
```

Here's a simple example of using the above-defined plugin to return the value of "pi":

```{code-cell} python
---
id: pi
---
import math

math.pi
```

{pi}

And here's a somewhat more involved example which creates a sine wave and renders it via plotly:

```{code-cell} python
---
id: sinwave
---
import numpy as np
x = np.linspace(0, 2.0 * np.pi, 100)
y = np.sin(x)
[{'x': x, 'y': y}]
```

And here's its output in a Plotly graph:

<Plotly data={sinwave} />