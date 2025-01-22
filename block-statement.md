# Use block Statements to Implement Exception Handling

## Why Use Them:

To group tasks logically.
To define rescue and always blocks for error handling and cleanup.
To ensure tasks run even in case of failure or to perform mandatory cleanup.

## Example Scenario: Handling a failed file copy operation.
```

- name: Example of block, rescue, and always
  hosts: all
  tasks:
    - block:
        - name: Copy file to destination
          copy:
            src: /path/to/source_file
            dest: /path/to/destination

        - name: Ensure file permissions
          file:
            path: /path/to/destination
            mode: '0644'

      rescue:
        - name: Log failure
          debug:
            msg: "Failed to copy the file or set permissions!"

      always:
        - name: Clean up temporary files
          file:
            path: /tmp/temp_file
            state: absent
```
## Explanation:

Tasks in the block section execute as usual.
If any task in the block fails, the rescue section executes.
The always section runs regardless of success or failure, ensuring cleanup.
