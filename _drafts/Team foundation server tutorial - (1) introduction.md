---
layout: post
title: Team foundation server tutorial (1)
author: Andy Feng
---

# Introduction #
Version control systems are software that help us track changes we make in our code over time. As we edit to our code, we tell the version control system to take a snapshot of our files. The version control system saves that snapshot permanently so we can recall it later if we need it.

![](/images/posts/20180528-tfs-10.png)

`Team Foundation Server (TFS)` is a collaboration tool including version control, . It is integrated with Visual Studio and enables a team to work together and organize efforts to complete a project. Dot Net developers use TFS for source control, bug tracking, requirement gathering and to manage complete lifecycle of software development.

![](/images/posts/20180528-tfs-9.png)

# Structure of a team project

- src: source code of the complete solution
- db: ddl, stored procedure, sql query scripts
- doc: design document, installation manual, user manual, - references
- resources: 3rd libs, dlls, runtime tools

# Fundamentals
There are some terminologies of TFS:

`team project` is an indiviaul project of a team.
`collection` is a group of related projects.
![](/images/posts/20180528-tfs-11.png)

`workspace` is a a mapping of one or more local working folder(s) to one or more source control folder(s) in TFS. TFS syncs the client and the server based on workspace. 
![](/images/posts/20180528-tfs-12.png)

# Check out a team project
1. Connect a team foundation server
    visual studio > team > manage connection > servers > add a new connection to tfs collection

    ![](/images/posts/20180528-tfs-1.png)

    ![](/images/posts/20180528-tfs-2.png)

    ![](/images/posts/20180528-tfs-3.png)

    connected successfully

1. Connect to a team project    
    select a team project > connect
    ![](/images/posts/20180528-tfs-4.png)

    Now we've defined our connection to team foundation server. The next thing we need to do is create a workspace so that we can begin performing operations.

1. Create a workspace

    A workspace defined what code will be brought from the server to the client and where on the client the code will live. It is essentially a mapping between server and client, and determines how server and client sync.

    vs > team explorer > source control explorer
    ![](/images/posts/20180528-tfs-5.png)

    method1: 

    local path > click not mapped > specify a path
    ![](/images/posts/20180528-tfs-6.png)

    method2: 

    workspace > workspaces > add or edit > add or edit the mapping of team project(s) under a collection between server and client
    ![](/images/posts/20180528-tfs-7.png)

1. Download source code
    vs > team explorer > source control explorer > left panel of folders > select a team project > right click > get latest version

    ![](/images/posts/20180528-tfs-8.png)

    By default, we grab the latest code and sync client with server. We can download code via multiple ways:

    - latest (default)
    - by specific version
    - by date
    - by label
    - by workspace version

# Check in a team project
1. add/edit file(s)
    add some files in the folder:
    ![](/images/posts/20180528-tfs-13.png)

    vs > source control explorer > project > right click > add items
    ![](/images/posts/20180528-tfs-14.png)

    ![](/images/posts/20180528-tfs-15.png)

1. check in changes
    team explorer > pending changes
    ![](/images/posts/20180528-tfs-4.png)

    add comment > checkin
    ![](/images/posts/20180528-tfs-16.png)

# More commands
1. Add tfs ignore
    We can configure which kinds of files are ignored by placing text file called `.tfignore` in the folder where we want rules to apply. The effects of the `.tfignore` file are recursive. Also, we can create `.tfignore` files in sub-folders to override the effects of a `.tfignore` file in a parent folder. This file helps us check in clean code to server.

    Here is a ignore configuration for visual studio project:

    ```
    # User-specific files
    *.suo
    *.user
    *.sln.docstates

    # Build results
    bin
    obj

    #include nuget executable
    !NuGet.exe

    #nuget packages directory
    packages

    #include package target files which may be required for msbuild
    !packages/*.targets

    # ReSharper
    _ReSharper*/
    *.ReSharper

    # TeamCity
    _TeamCity*

    # DotCover is a Code Coverage Tool
    *.dotCover

    # Click-Once directory
    publish/

    # Publish Web Output
    *.Publish.xml
    *.pubxml

    # Backup & report files from converting an old project file to a newer
    _UpgradeReport_Files/
    Backup*/
    UpgradeLog*.XML
    UpgradeLog*.htm

    # Windows image file caches
    Thumbs.db
    ehthumbs.db

    # Folder config file
    Desktop.ini

    # VS 2015 folder
    .vs

    # node
    node_modules/

    ```

1. View changes of team collection, team project, a folder or a file. Compare different versions of a file.
    ![](/images/posts/20180528-tfs-17.png)

# References
[https://docs.microsoft.com/en-us/vsts/tfvc/?view=vsts](https://docs.microsoft.com/en-us/vsts/tfvc/?view=vsts)

[https://docs.microsoft.com/en-us/vsts/tfvc/add-files-server](https://docs.microsoft.com/en-us/vsts/tfvc/add-files-server)