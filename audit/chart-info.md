# Chart details

## Treemap
![Sandworm Treemap](https://assets.sandworm.dev/showcase/treemap-snip.png)
* [Sample treemap for Express@4.18.2](https://assets.sandworm.dev/charts/npm/express/4.18.2/treemap.svg)
* Node colors represent the dependency depth;
* Node surface represents the size of the corresponding directory under `node_modules`;
* A dotted pattern in a node background means the package is a shared dependency, required by multiple packages, and present multiple times in the chart;
* Shared dependency sizes are added to every dependent package, to represent the independent size structure properly; hence, the displayed size might be larger than the actual size on disk;
* A red package background means the package has direct vulnerabilities;
* A purple package background means the package depends on other vulnerable packages;
* Click on a node to make the tooltip persist; click outside to close it;
* When representing deep dependencies, the surface area of certain packages might reach zero, making them invisible.

## Tree
![Sandworm Tree](https://assets.sandworm.dev/showcase/tree-snip.png)
* [Sample tree for Express@4.18.2](https://assets.sandworm.dev/charts/npm/express/4.18.2/tree.svg)
* Nodes are grouped by color based on the root dependency that they belong to;
* Red text in a package name means the package has direct vulnerabilities;
* Purple text in a package name means the package depends on other vulnerable packages;
* Click on a node to make the tooltip persist; click outside to close it;
* By default, the tree chart has a maximum depth of 7, meaning only seven levels of dependencies get represented, to keep the output readable; you can override this using the `--md` option.