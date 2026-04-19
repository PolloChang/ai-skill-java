# Java 21 + Spring Boot + Picocli 程式碼範例

這裡展示了本專案認可的標準程式碼寫法。AI 在生成程式碼時應參考這些範例。

## 1. Domain / Entity 與 DTO (Java 21 Record)

```java
package com.master.ai.domain;

import jakarta.persistence.*;
import java.time.Instant;

@Entity
@Table(name = "tasks")
public class Task {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String description;

    @Column(nullable = false)
    private Instant createdAt = Instant.now();

    // 建構子與 Getter/Setter (可使用 Lombok)
    public Task() {}
    
    public Task(String description) {
        this.description = description;
    }
    
    public String getDescription() { return description; }
}

// DTO 使用 Record
public record TaskResponse(Long id, String description, Instant createdAt) {
    public static TaskResponse fromEntity(Task task) {
        return new TaskResponse(task.getId(), task.getDescription(), task.getCreatedAt());
    }
}
```

## 2. Spring Boot Service (Virtual Threads 準備)

```java
package com.master.ai.service;

import com.master.ai.domain.Task;
import com.master.ai.domain.TaskResponse;
import com.master.ai.repository.TaskRepository;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

@Service
public class TaskService {

    private final TaskRepository taskRepository;

    // 建構子注入
    public TaskService(TaskRepository taskRepository) {
        this.taskRepository = taskRepository;
    }

    @Transactional(readOnly = true)
    public List<TaskResponse> getAllTasks() {
        return taskRepository.findAll().stream()
                .map(TaskResponse::fromEntity)
                .toList(); // Java 16+ 的簡潔寫法
    }

    @Transactional
    public TaskResponse createTask(String description) {
        var task = new Task(description);
        var savedTask = taskRepository.save(task);
        return TaskResponse.fromEntity(savedTask);
    }
}
```

## 3. Picocli Command 整合 Spring Boot

```java
package com.master.ai.cli;

import com.master.ai.service.TaskService;
import org.springframework.stereotype.Component;
import picocli.CommandLine.Command;
import picocli.CommandLine.Option;

import java.util.concurrent.Callable;

@Component
@Command(name = "task-cli", description = "管理您的任務", mixinStandardHelpOptions = true)
public class TaskCommand implements Callable<Integer> {

    private final TaskService taskService;

    public TaskCommand(TaskService taskService) {
        this.taskService = taskService;
    }

    @Option(names = {"-a", "--add"}, description = "新增一個任務")
    private String newTaskDescription;

    @Option(names = {"-l", "--list"}, description = "列出所有任務")
    private boolean listTasks;

    @Override
    public Integer call() {
        // 使用 Java 21 Switch Pattern 處理邏輯 (簡化示意)
        if (newTaskDescription != null) {
            var task = taskService.createTask(newTaskDescription);
            System.out.println("✅ 成功建立任務: " + task.description());
            return 0;
        }

        if (listTasks) {
            var tasks = taskService.getAllTasks();
            if (tasks.isEmpty()) {
                System.out.println("沒有找到任何任務。");
            } else {
                tasks.forEach(t -> System.out.printf("[%d] %s%n", t.id(), t.description()));
            }
            return 0;
        }

        System.out.println("請輸入有效參數。使用 --help 查看說明。");
        return 1;
    }
}
```