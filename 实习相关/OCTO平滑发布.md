### 发布过程中OCTO节点状态的变化

状态变化：正常 -> 启动中 -> 未启动 -> 正常

- 正常 -> 启动中：plus调用octo的接口将节点变为启动中。客户端不再调用启动中的服务端。
- 启动中 -> 未启动：业务项目中check.sh校验通过后，plus调用octo的接口将节点变为未启动。
- 未启动 -> 正常：octo健康检查中心扫描节点正常后，将节点变为正常。备注：健康检查中心只扫描未启动节点，不扫描启动中节点。



<img src="C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20240826212458916.png" alt="image-20240826212458916" style="zoom:100%;" />

1. 禁用Octo流量：将Octo的节点状态从“正常”改成“启动中”，并等待30s左右给Octo做流量摘除 
2. 发布：发布服务 
3. plus检查：plus调用业务配置的check.sh脚本 
4. 流量恢复：plus将octo节点状态改成“未启动”，而后由octo的scanner检查ip:port是否健康，如果OK，会自动将octo的节点状态改成“正常”，从而恢复流量 