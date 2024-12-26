**容错性**

**任务描述**  
基于实验报告 #2，实现在系统中增加容错机制的功能。

**要求**  
1. 在 Gateway Service 上为所有的读取操作实现 Circuit Breaker 模式。统计数据存储在内存中，如果系统连续 N 次没有响应，那么在第 N + 1 次请求时，直接返回 fallback，而不是发起请求。通过小的超时进行实际系统请求，检查其状态。
   
2. 在每个服务上创建一个特殊的端点 `GET /manage/health`，返回 200 OK，用于检查服务的可用性（在 Github Actions 的 `wait-script.sh` 脚本和 `test-script.sh` 测试中使用）。

   请求示例：
   ```
   "$path"/wait-for.sh -t 120 "http://localhost:$port/manage/health" -- echo "Host localhost:$port is active"
   ```

3. 当从非核心的数据源（非主数据源）获取数据失败时，返回 fallback 响应。根据具体情况，这些返回可以是：
   - 空对象或数组；
   - 带有已填充字段（如 uid 或类似字段）的对象，用于与其他系统的交互；
   - 默认字符串（如果此时没有更改变量的类型）。

4. 该任务描述了两个会改变多个系统状态的操作。如果参与这些操作的某个系统不可用，应该执行：
   - 撤销整个操作；
   - 返回用户操作成功完成的响应，并将此请求放入 Gateway Service 的队列中，等待重新执行。

5. 对于自动化测试的执行，在 `autograding.json` 和 `classroom.yml` 文件中，将 `<variant>` 替换为你的具体任务编号。

6. 在 `docker-compose.yml` 文件中配置 Docker 容器的构建和启动。

7. 代码需要存储在 Github 上，构建使用 Github Actions。

8. 每个服务都需要打包成 Docker 容器。

9. 在 `classroom.yml` 文件中添加构建和执行单元测试的步骤。

**说明**  
- 本地开发中可以使用 Docker 中的 Postgres 数据库。
- 服务间的交互方式保持与实验报告 #2 中一致。
- 队列的实现可以使用本地语言的原生实现（例如，Java 中的 `BlockingQueue`），或者使用现成的队列实现，如 RabbitMQ、Redis、ZeroMQ 等。强烈不推荐使用关系型数据库来模拟队列。
- 可以使用外部队列或在 Docker 中运行队列。
- 容错性测试使用 Docker 容器的停止和启动，通过 `test-script.sh` 脚本实现。需要从项目根目录执行该脚本，因为它会根据任务编号访问 Postman 文件夹。

**测试脚本示例：**  
```
# 启动测试场景：
# * <variant> – 任务编号（v1 | v2 | v3 | v4）
# * <service> – Docker Compose 中的服务名称
# * <port>    – 服务运行的端口
$ scripts/test-script.sh <variant> <service> <port>
```

**任务提交要求**  
- 在你接受任务时，GitHub 上会为你的用户创建该仓库的 fork。
- 所有测试成功执行后，GitHub Classroom 的 Dashboard 上会标记测试成功。

**任务编号分配**  
任务编号的分配与实验报告 #2 中的分配方式相同。

- Flight Booking System（航班预订系统）
- Hotels Booking System（酒店预订系统）
- Car Rental System（汽车租赁系统）
- Library System（图书馆系统）
