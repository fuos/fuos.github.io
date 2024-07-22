---
title: Hello World
abbrlink: 4a17b156
date: 2024-07-05 10:23:18
tags:
  - Hexo
categories:
  - 建站
katex: true
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Quick Start

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

### katex

$concurrency = cost_\text{avg} \times qps \quad (1)$

$$
\begin{equation}
\mathcal{F} = \begin{cases}
    \infty & \text{if } \mathcal{M}_{measure} < \alpha \times \mathcal{M}_{expect} \\
    \mathcal{M}_\text{expect} / \mathcal{M}_\text{measure} & \text{if } \alpha \times \mathcal{M}_{expect} < \mathcal{M}_{measure} < \mathcal{M}_{expect} \\
    \sqrt{\frac{\mathcal{M}_{expect}}{\mathcal{M}_{measure}}} & \text{if } \mathcal{M}_{measure} \ge \mathcal{M}_{expect}
\end{cases}
\end{equation}
$$

### 图表

{% mermaid %}
pie
    title Key elements in Product X
    "Calcium" : 42.96
    "Potassium" : 50.05
    "Magnesium" : 10.01
    "Iron" :  5
{% endmermaid %}

### tabs

{% tabs test1 %}
<!-- tab -->
**This is Tab 1.**
<!-- endtab -->

<!-- tab -->
**This is Tab 2.**
<!-- endtab -->

<!-- tab -->
**This is Tab 3.**
<!-- endtab -->
{% endtabs %}

More info: [Deployment](https://hexo.io/docs/one-command-deployment.html)
