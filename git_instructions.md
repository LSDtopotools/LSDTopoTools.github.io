==============================================
Using Git
==============================================

  Most of the Land Surface Dynamics group's code is under the version control
  of `subverion <http://svnbook.red-bean.com/>`_, and housed within the 
  `sourceEd <https://sourced.ecdf.ed.ac.uk/projects/geos/LSD_devel/>`_. respositories. 

  However it can be advantageous to distribute code for open development on 
  the `Git platform <http://git-scm.com/>`_ and distributing the code via 
  `Github <https://github.com/>`_. 
  
  In this document we detail how to use Git on the Edinburgh servers. 
  
Getting started with Git
==============================================

  Git is already installed on our servers. You can call it with::
  
    $ git
    
  Much of what I will descrbe below is also described in the
  `Git book <http://git-scm.com/book/en/>`_.
  
  The first thing you want to do is set the username and email for the session::
  
    $ git config --global user.name "John Doe"
    $ git config --global user.email johndoe@example.com   
    
  I'm not acutally sure if this will affect other users, as it is sitting on the 
  GeoScience server. So you might choose to not use the --global flag, and 
  so the name and email will only apply to the specific project you are working 
  on that session::
  
    $ git config user.name "John Doe"
    $ git config user.email johndoe@example.com     
    
  After trying this, I get an error::
  
    error: could not lock config file .git/config: No such file or directory
    
  So so much for that. 
  
  I'll try something else. First, I'll navigate to the directory that holds my
  prospective git repository::
  
    smudd@burn OneD_hillslope $ pwd
    /home/smudd/SMMDataStore/Git_projects/OneD_hillslope
    smudd@burn OneD_hillslope $ ls
    branches  tags  trunk
    
  Now I'll initiate git here::
  
    smudd@burn OneD_hillslope $ git init
    Initialized empty Git repository in /exports/csce/datastore/geos/users/smudd/Git_projects/OneD_hillslope/.git/

  Great! That seemed to work. Lets try that local configuration again::
  
    smudd@burn OneD_hillslope $ git config --local user.name simon.m.mudd
    smudd@burn OneD_hillslope $ git config user.name
    simon.m.mudd
    
  Excellent!! Things are moving nicely along now! I then set up some other config options::
  
    smudd@burn OneD_hillslope $ git config --local merge.tool vimdiff
    smudd@burn OneD_hillslope $ git config --local core.editor emacs

  You can check all your options with::
  
    smudd@burn OneD_hillslope $ git config --list
    core.repositoryformatversion=0
    core.filemode=true
    core.bare=false
    core.logallrefupdates=true
    core.editor=emacs
    user.name=simon.m.mudd
    user.email=Mudd.Pile@pileofmudd.mudd
    merge.tool=vimdiff

Adding files and directories to the repository
============================================================

  So now you gave run ``git init`` in some folder to initiate a repository. 
  You will now want to add files with the add command::
  
    smudd@burn OneD_hillslope $ ls
    branches  tags  trunk
    smudd@burn OneD_hillslope $ git add tags
    smudd@burn OneD_hillslope $ git add trunk
    
  So a few things here
  
   * I think that the ``branches`` folder is redundant since ``git`` does this implicitly
   * ``Git`` adds all files in a folder, including all the files in a named subdirectoy. 
   
  If you want to add a specific file(s), you can do something like this::       
 
    smudd@burn OneD_hillslope $ git add trunk/*.hpp
 
Committing to the repository
============================================================  

  This works much the same as svn::
  
    mudd@burn OneD_hillslope $ git commit -m "Initial project version" .
    
  Where the . indicates you want everything in the current directory including subfolders.
  
Pushing your repository to Github
=============================================================

  `Github <https://github.com/>`_ is a resource that hosts git repositories. 
  It is a popular place to put open source code. 
  
  To host a repository on `Github <https://github.com/>`_, you will need to set up the repository before
  synching your local repository with the github repository. 
  
  You can place your repository on 
  `Github <https://github.com/>`_ by using the ``push`` command::
  
    smudd@burn OneD_hillslope $ git remote add origin https://github.com/simon-m-mudd/OneD_hillslope.git
    smudd@burn OneD_hillslope $ git push -u origin master
    Counting objects: 36, done.
    Delta compression using up to 64 threads.
    Compressing objects: 100% (33/33), done.
    Writing objects: 100% (36/36), 46.31 KiB, done.
    Total 36 (delta 8), reused 0 (delta 0)
    To https://github.com/simon-m-mudd/OneD_hillslope.git
    * [new branch]      master -> master
    Branch master set up to track remote branch master from origin.
    
  Once you have uploaded an inital copy, you will need to keep it in sync with 
  local copies. You can push things to github with::
  
    git push -u origin master
    
  but if things are out of sync (I did this by modifying a readme on the github site)
  you will get this error message::
  
    To https://github.com/simon-m-mudd/OneD_hillslope.git
    ! [rejected]        master -> master (non-fast-forward)
    error: failed to push some refs to 'https://github.com/simon-m-mudd/OneD_hillslope.git'
    hint: Updates were rejected because the tip of your current branch is behind
    hint: its remote counterpart. Merge the remote changes (e.g. 'git pull')
    hint: before pushing again.
    hint: See the 'Note about fast-forwards' in 'git push --help' for details. 
    
  I fixed this by running::
  
    git pull origin

  It seemed to be happy after that. 
         
Problems with Setting up repos on github
==========================================
  
 Git is not quite as intuitive as subversions, so I've found quite a number of problems in setting up github repos. Here are some examples and (hopefully) their fixes. 
 
Creating local repo and then Github repository
------------------------------------------------

  I made a local github repository using::
  
    git init
    
  And then tried to push to a github repo, but the first error message is you need to make a repository on github first. 
  I added a readme file on Github, but this seemed to lead to errors::
  
    Updates were rejected because the tip of your current branch is behind
    hint: its remote counterpart. Merge the remote changes (e.g. 'git pull')
    
  So what I did to fix this was:
  
    #. On the local repo, I used::
    
        touch README.md
	git add README.md
        git commit -m "Trying to add readme" .
    
    #. Then I pulled from the master::
    
        git pull origin master
        
        
    #. Then I pushed to the master. That seemed to fix things::
    
        git push -u origin master

 
 


