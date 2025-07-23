#include <stdio.h>   
#include <stdlib.h>  
#include <string.h>  
#define MAX_TASK_LENGTH 256
typedef enum {
    PENDING,
    COMPLETED
} TaskStatus;
typedef struct {
    char description[MAX_TASK_LENGTH];
    TaskStatus status;
} Task;
Task *taskList = NULL;
int taskCount = 0;
int taskCapacity = 0; 
void displayMenu();
void addTask();
void viewTasks();
void markTaskCompleted();
void removeTask();
void clearInputBuffer();
int main() {
    int choice;
    taskCapacity = 5;
    taskList = (Task *)malloc(taskCapacity * sizeof(Task));
    if (taskList == NULL) {
        perror("Failed to allocate initial memory for tasks");
        return 1; 
    }

    do {
        displayMenu();
        printf("Enter your choice: ");
        if (scanf("%d", &choice) != 1) { 
            printf("Invalid input. Please enter a number.\n");
            clearInputBuffer(); 
            continue; 
        }
        clearInputBuffer(); 

        switch (choice) {
            case 1:
                addTask();
                break;
            case 2:
                viewTasks();
                break;
            case 3:
                markTaskCompleted();
                break;
            case 4:
                removeTask();
                break;
            case 5:
                printf("Exiting To-Do List Manager. Goodbye!\n");
                break;
            default:
                printf("Invalid choice. Please try again.\n");
                break;
        }
        printf("\n"); 
    } while (choice != 5);
    free(taskList);
    taskList = NULL; 
    return 0; 
}
void displayMenu() {
    printf("--- To-Do List Manager ---\n");
    printf("1. Add Task\n");
    printf("2. View Tasks\n");
    printf("3. Mark Task as Completed\n");
    printf("4. Remove Task\n");
    printf("5. Exit\n");
}
void addTask() {
    if (taskCount == taskCapacity) {
        taskCapacity *= 2; 
        Task *temp = (Task *)realloc(taskList, taskCapacity * sizeof(Task));
        if (temp == NULL) {
            perror("Failed to reallocate memory for tasks");
            printf("Warning: Could not add more tasks due to memory issues.\n");
            return;
        }
        taskList = temp;
        printf("Task list capacity increased to %d.\n", taskCapacity);
    }

    printf("Enter task description (max %d chars): ", MAX_TASK_LENGTH - 1);
    if (fgets(taskList[taskCount].description, MAX_TASK_LENGTH, stdin) == NULL) {
        perror("Error reading task description");
        return;
    }
    taskList[taskCount].description[strcspn(taskList[taskCount].description, "\n")] = '\0';

    taskList[taskCount].status = PENDING; 
    taskCount++;
    printf("Task added successfully!\n");
}

void viewTasks() {
    if (taskCount == 0) {
        printf("No tasks in the list.\n");
        return;
    }

    printf("\n--- Your To-Do List ---\n");
    for (int i = 0; i < taskCount; i++) {
        printf("%d. [%s] %s\n",
               i + 1, 
               (taskList[i].status == COMPLETED ? "COMPLETED" : "PENDING  "),
               taskList[i].description);
    }
}

void markTaskCompleted() {
    if (taskCount == 0) {
        printf("No tasks to mark.\n");
        return;
    }
    viewTasks();
    int taskNumber;
    printf("Enter the number of the task to mark as completed: ");
    if (scanf("%d", &taskNumber) != 1) {
        printf("Invalid input. Please enter a number.\n");
        clearInputBuffer();
        return;
    }
    clearInputBuffer();

    if (taskNumber < 1 || taskNumber > taskCount) {
        printf("Invalid task number.\n");
        return;
    }
    if (taskList[taskNumber - 1].status == COMPLETED) {
        printf("Task \"%s\" is already marked as completed.\n", taskList[taskNumber - 1].description);
    } else {
        taskList[taskNumber - 1].status = COMPLETED;
        printf("Task \"%s\" marked as completed!\n", taskList[taskNumber - 1].description);
    }
}

void removeTask() {
    if (taskCount == 0) {
        printf("No tasks to remove.\n");
        return;
    }
    viewTasks(); 
    int taskNumber;
    printf("Enter the number of the task to remove: ");
    if (scanf("%d", &taskNumber) != 1) {
        printf("Invalid input. Please enter a number.\n");
        clearInputBuffer();
        return;
    }
    clearInputBuffer();

    if (taskNumber < 1 || taskNumber > taskCount) {
        printf("Invalid task number.\n");
        return;
    }
    for (int i = taskNumber - 1; i < taskCount - 1; i++) {
        taskList[i] = taskList[i + 1];
    }
    taskCount--; 
    printf("Task removed successfully!\n");
}
void clearInputBuffer() {
    int c;
    while ((c = getchar()) != '\n' && c != EOF);
}
