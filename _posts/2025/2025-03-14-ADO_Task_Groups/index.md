---
layout: post
title: "Task Groups in Azure DevOps (ADO): From Automation to Exploitation"
categories: Proxy Tools
---

# Task Groups 
Task Groups is a feature in Azure DevOps (ADO) to create re-usable task(s) within a (classic) pipeline. This allows you to add that Task Group in another pipeline via the task catalog and edit that task group in one place rather then in each instance. Task Groups save time and reduce duplication of efforts. Reference the docs [Task group (Classic)](https://learn.microsoft.com/en-us/azure/devops/pipelines/release/task-groups?view=azure-devops) for further shenanigans.

## How do you create a Task Group?
You have to start from pipeline.
1. Use the "➕" button to Add a task
2. Give your task(s) a title
3. Perform whatever changes to your task(s). For example, with a PowerShell Task you might be adding a script to perform some action.
4. Select the task(s) you want in your Task Groups
5. Right-click on one of the selected tasks
6. Select "➕ Create task group"

> Note that **Task Groups** have their own unique icon that looks like a tiny box on top of a bigger box. This can help you identify them in Pipelines.

## Re-using the Task Group
Now you can go to another pipeline and re-use it:
1. Use the "➕" button to Add a task
2. Search for the name of the Task Group
3. Add it

## Other notes and nuances
- Naming scheme once you create the task group is: **`Task group: {task name}`**
- When you re-use the Task Group in another pipeline, you can change the display name. However, the original name will remain underneath the new display name for your reference.


# Security
You want to review the Task Groups permissions and use the principle of least privilege to give access to only the users that need and just the right level of access. 

### Review Permissions at Task groups parent level
1. Go to Pipelines -> Task groups
2. Click "🛡 Security"
3. Review the permissions for each listed item

### Review individual Task groups permissions
Since permissions could be changed at each object's level, you might also want to check the individual task groups, starting with the ones that have the potential for the largest blast radius.
1. Go to Pipelines -> Task groups
2. Select a Task group
3. Select the ellipsis "..."
4. Click "🛡 Security"
5. Review the permissions for each listed item

## 🔴 Possible attacks 🏴‍☠️
If you (or threat actors) have the **Edit Task Groups** permission, it would allow them to add or modify an existing script that performs malicious actions the next time any pipeline that uses that Task Group is executed. Since they can allow code execution within your pipeline, this could lead to poisoning your code (like putting in a backdoor, forward tokens to a C2, etc...), compromising the server where the agent is running, data exfiltration, access to identities within that box that may allow you further access or a pivot, access to their cloud environment, etc...

### Edit Task Group
1. Go to Pipelines -> Task groups
2. Select a target Task group of your choice
3. From here you can add "➕" to  Add task to task group (like a PowerShell script 😏) or edit an existing task with whatever your heart desires!
4. Click "💾 Save"

### Recon where the task group is used
1. Go to Pipelines -> Task groups
2. From here select the "References" tab. This will list Build, Release and other Task groups that leverage this Task group.
3. Review anything that looks interesting!
