**route**
路由块定义路由树中的节点及其子节点。如果未设置，则其可选配置参数将从其父节点继承。

每个警报都会在配置的顶级路由中进入路由树，该路由必须匹配所有警报（即没有任何已配置的匹配器）。然后它遍历子节点。如果`continue`设置为false，则在第一个匹配的子项后停止。如果`continue`在匹配节点上为true，则警报将继续与后续兄弟节点匹配。如果警报与节点的任何子节点都不匹配（没有匹配的子节点，或者不存在），则根据当前节点的配置参数处理警报。