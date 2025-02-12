---
title: 'What is Git Flow?'
# subtitle: "Blog post subtitle :zap:"
summary: Git Flow is an effective approach that systematizes branch management in version control systems. In this article, I will explain in detail what Git Flow is, how it's used, and best practice recommendations.
date: 2025-01-31  
cardimage: '/en/blog/git-flow/images/cover.png'
featureimage: 'en/blog/git-flow/images/cover.png'
featured: true
# caption: Image caption
categories: ['Git']
tags: ['git-flow', 'git']
translationKey: "git-flow-guide"
authors:
  - Çağatay: /images/author.jpeg
---

\
Hello. In this article, I will briefly discuss version control systems and explain in detail what Git Flow is and how it's used. Version control systems are important tools used in software development processes to track and manage code versions. Git Flow is a branching model that enables us to use this version control system more effectively.

## What is a Version Control System?

A version control system is a system that records step-by-step changes we make to one or more files, documents (software project, office documents, etc.), allows us to revert to a specific version later, and if desired, enables us to store and manage this in an online repository. Git, SVN, BitKeeper, and Mercurial are examples of version control systems.

### Why Do We Use Version Control Systems?

1. A complete long-term change history is kept for each file.

   - This means tracking every change made to the file by multiple people over the years.
   - This allows us to compare our old and new code to understand how we got to where we are.

2. Allows team members to work simultaneously on the same code.

   - It's possible to create sub-versions to carry out different work on the software and later integrate it into the main software.

3. Enables tracking every change made to the software and linking it to project management.

   - Allows software issues to be associated with and tracked through versions.

4. Allows us to revert to old code records when we encounter errors in the project.

### Version Control Systems

**Local VCS:** This is the oldest version control system approach. Our project and the changes we make are stored in a database on the user machine. Each commit is stored as a version, and each version is distinguished by assigning a hash value to the commit. It also provides version viewing capability. However, only one user can work effectively in this system.

**Centralized VCS:** This is a versioning system created for multiple people to work effectively on a project. CVS and SVN are centralized version control systems. In this system, the project is kept in a shared repository, and multiple developers perform checkout and commit operations on the same repository. While this method allows everyone to contribute to the project, it has some serious problems. If the single central server fails for 1 hour, users won't be able to save their work or versioned copies of their project for that hour.

**Distributed VCS:** This is a version system created due to the limitations of centralized version systems, such as developers' inability to work offline and difficulty in recovery if the repository is damaged. Systems like Git, Mercurial, and BitKeeper are examples of distributed version systems. In these systems, there is no central repository, and each machine working on the project keeps a copy of the project on their local computer. Developers don't need to communicate with the remote repository when they want to make changes to the project or view project history. If one server crashes and there are systems working collaboratively on that server, the system can be recovered by having one of the developers restore the project to the server. In summary, it allows different developers on the same project to work in different ways with different workflows.

### What is Git Flow?

On January 5, 2020, nvie proposed a model for keeping git repositories organized in a post at <a href="https://nvie.com/posts/a-successful-git-branching-model/" rel="nofollow">https://nvie.com/posts/a-successful-git-branching-model/</a>. Later, he released a project called Git-Flow that includes git extensions to make using this model easier. The GitFlow model is fundamentally based on the git version control system. In other words, it's possible to execute all model operations with git commands.

### Advantages and Disadvantages of Git Flow

**Advantages:**

- Provides an organized and predictable development process
- Offers an ideal structure for large teams
- Simplifies version management
- Each branch has a clear purpose
- Supports parallel development

**Disadvantages:**

- Can be too complex for small projects
- May not be suitable for continuous delivery
- Branch structure can sometimes lead to unnecessary complexity
- Requires additional tool installation
- Learning curve is higher compared to other models

### Alternatives to Git Flow

- **GitHub Flow:** A simpler model, uses only master and feature branches
- **GitLab Flow:** Strikes a balance between Git Flow and GitHub Flow
- **Trunk Based Development:** Focuses on development on the main branch

### Git Flow Working Principle

{{< figure src="./images/diagram.png" alt="git flow">}}

Git Flow model has 5 main branches:

- **master:** Master, one of the main branches, exists throughout the project. The master branch always contains code that can be deployed to production. Ideally, each commit to the master branch is a version and should be marked with "git tag" (given a version number). Direct commits to the master branch are not made; only merges from hotfix and release branches are allowed.

- **develop:** Develop is another main branch that exists throughout the project. The develop branch contains changes made for the next version. All feature branches are first merged into this branch. This branch is the main development branch of the project, and continuous integration (CI) processes typically run on this branch.

- **hotfix:** The hotfix branch is used when there's a critical bug in the live version that needs to be fixed and deployed immediately. The hotfix branch is created from the master branch and is typically named in the format 'hotfix/[version]-[description]'. When the bug fix is completed in the hotfix branch, this branch is merged with both Developer and Master. After merging with Master, the change is tagged with a new version number.

- **feature:** When adding a new feature, a Feature branch is created for this feature. Feature branches are always created from the develop branch and are typically named in the format 'feature/[feature-name]'. These can be considered as changes relative to features. Multiple feature branches can be opened simultaneously. This means different developers can work on different features. Developing features in separate branches both prevents the Develop branch from being filled with unnecessary commits and makes it easy to abandon a feature by simply deleting the feature branch. When the feature is complete, this branch is merged with the Develop branch and the feature branch is deleted. So feature branches only live during development. Of course, during this process, you may need to occasionally pull from the Develop branch for checking purposes because another developer might have finished their feature branch first and version might have been pushed to the Develop branch. Feature branches should not include names containing master, release, develop, or hotfix.

- **release:** Let's say all changes are complete. When a new version is to be released, a new Release branch is created from the Develop branch. Release branches are typically named in the format 'release/[version]'. Final changes in the version, changing version numbers, etc. are performed in this branch. Only bug fixes should be made in the Release branch, new features should not be added. When all necessary changes are completed, all changes completed in the Release branch are merged into both Master and Develop branches. The version number is tagged with git tag in the Master branch, and then the Release branch is deleted.

### Git Flow Example Project

- **/brew install git-flow > git flow init**
  {{< figure src="./images/git-flow-init.png" alt="git flow">}}
  First, we install with "brew install git-flow". GitFlow doesn't come with git. It needs to be installed separately. This is considered one of its disadvantages. In git, we used the "git init" command to start the project. For git-flow, we enter the "git flow init" command to start the git-flow process. When the command runs, if no repo exists, it first creates a repo. Then it asks the user for branch names to be used for the process. Branch names can be customized, but it's recommended to keep the default values.

- **/git flow feature start performance**
  {{< figure src="./images/git-flow-feature-start.png" alt="git flow">}}
  This command creates a new feature branch. Since the feature name is performance, the default branch will be feature/performance. We can do the same thing with the existing git command. The command we would need to enter for this would be "git checkout -b myFeature feature/performance".

- **/git flow feature finish performance**
  {{< figure src="./images/git-flow-feature-finish.png" alt="git flow">}}
  This command closes a previously opened branch. The closing process starts with merging the feature branch into the develop branch and ends with deleting the feature branch. If changes are not committed when the command is run, it will give an error. If push is not done after commit, it will give an error. To do this operation with normal git commands, first commit is made in the relevant branch, then "git checkout develop > git merge --no-ff feature/performance > git branch -D feature/performance" commands are run in order.

- **/git flow release start 1.0.0**
  {{< figure src="./images/git-flow-release-start.png" alt="git flow">}}
  When this command is entered, a new version becomes ready for release. When the command is run, a new release/1.0.0 branch is created from the Develop branch. To perform this operation with the existing git command, the "git checkout -b release/1.0.0 develop" command is run.

- **/git flow release finish 1.0.0**
  {{< figure src="./images/git-flow-release-finish.png" alt="git flow">}}
  When the command is entered, the completed version is moved to the master branch. The changes are merged with both develop and master branches. The last commit in the master branch is tagged with the version number. Then the release branch is automatically deleted. To do the same operation with git commands, run the following commands in order:

  ```bash
  git checkout master
  git merge --no-ff release/1.0.0
  git tag -a 1.0.0
  git checkout develop
  git merge --no-ff release/1.0.0
  git branch -d release/1.0.0
  ```

- **/git flow hotfix start 1.0.1**
  {{< figure src="./images/git-flow-hotfix-start.png" alt="git flow">}}
  A new hotfix is started with this command. Hotfix branches are used for urgent updates and are created from the master branch. When the command runs, a new hotfix/1.0.1 branch branching from the master branch is created. To do the operation with git command, run "git checkout -b hotfix/1.0.1 master" command.

- **/git flow hotfix finish 1.0.1**
  {{< figure src="./images/git-flow-hotfix-finish.png" alt="git flow">}}
  The hotfix is completed with this command. Changes are taken to both Develop and Master branches. The Master branch is tagged with 1.0.1 and the hotfix branch is deleted. When the "git tag -l" command is run, version numbers are displayed. To do the same operation with existing git commands, run the following commands in order:

  ```bash
  git checkout master
  git merge --no-ff hotfix/1.0.1
  git tag -a 1.0.1
  git checkout develop
  git merge --no-ff hotfix/1.0.1
  git branch -d hotfix/1.0.1
  ```

  ### Git Flow Best Practice Recommendations

1. **Branch Naming Conventions**

   - Use descriptive names for feature branches (e.g., feature/user-authentication)
   - Use semantic versioning for hotfix and release branches

2. **Commit Messages**

   - Write descriptive commit messages
   - Follow the Conventional Commits standard
   - Ensure each commit has a single purpose

3. **Code Review Process**

   - Perform code review before merging feature branches
   - Use automated testing processes
   - Don't forget documentation updates

4. **Merge Strategy**
   - Use the --no-ff (no fast-forward) parameter
   - Resolve merge conflicts quickly
   - Consider using squash commits

### Conclusion

Git Flow is an effective approach that systematizes branch management in modern software development processes. Through this workflow, teams can work more organized, better control versioning, and improve code quality. Especially in large projects and team collaborations, the structured branch strategy offered by Git Flow significantly improves development processes. By following the best practices mentioned above, you can successfully implement Git Flow in your project and make your software development processes more efficient.
