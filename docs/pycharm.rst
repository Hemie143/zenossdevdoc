.. _pycharm

*************
PyCharm setup
*************

.. _pycharm_ssh

==============
SSH Deployment
==============

1.	In PyCharm, go to : Tools -> Deployment -> Configuration
2.	Under the Connection tab, select the type (SFTP), enter the host, username and authentication type.
3.  Under the Mappings tab, enter the local path and deployment (remote) patch.
4.  Click on OK to leave the Configuration box.
5.	Synchronize with Tools -> Deployment -> Sync with Deployed to…

.. _pycharm_github

==================
Github Integration
==================

1.	Select VCS -> Enable Version Control Integration
2.	Select Git and click on OK
3.	Select root folder and select VCS -> Git -> Add
4.	Select VCS -> Git -> Commit Directory
5.	Enter a commit message and click on Commit.
6.	Select VCS -> Import into Version Control -> Share Project on GitHub
7.	Click on Continue

.. _pycharm_libs

=========
Libraries
=========

1.	Copy the content of /opt/zenoss/Products locally to c:\repos\zenoss.Products\Products
2.	Copy the content of /opt/zenoss/lib/python locally to c:\repos\zenoss.python
3.	Copy the content of /opt/zenoss/ZenPacks/ZenPacks.zenoss.PythonCollector-* locally to c:\repos\ZenPacks.zenoss.PythonCollector
4.	In PyCharm, go to File -> Settings.
5.	Select Project -> Project Interpreter.
6.	Select Python 2.7.12, click on cogwheel and select More.
7.	Click on the last button "Show paths for the selected interpreter"
8.	Add the following content roots:
    a.	c:\repos\zenoss.Products
    b.	c:\repos\zenoss.python
    c.	c:\repos\ZenPacks.zenoss.PythonCollector


