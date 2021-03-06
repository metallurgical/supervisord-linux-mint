# supervisord-linux-mint
Keep alive laravel queue's worker using supervisord

# Installation

**1) Method 1**

Using easy_install, which is a feature of setuptools. This is the preferred method of installation

    easy_install supervisor
    
**2) Method 2**

Using PIP :
 
     pip install supervisor
     
# Creating a Configuration File

This config file, having the Program/worker that can keep running always. You can create your own conf file intead.

Once the Supervisor installation has completed, run `echo_supervisord_conf`. This will print a “sample” Supervisor configuration file to your terminal’s stdout.

Once you see the file echoed to your terminal, reinvoke the command as `echo_supervisord_conf > /etc/supervisord.conf`. This won’t work if you do not have root access.

If you don’t have root access, or you’d rather not put the supervisord.conf file in /etc/supervisord.conf, you can place it in the current directory (echo_supervisord_conf > supervisord.conf) and start supervisord with the -c flag in order to specify the configuration file location.

For easy setup, just use below command :

    echo_supervisord_conf -c /etc/supervisord.conf  // this will create supervisord.conf file with sample configuration
    
If supervisord already running, it might throws out an error saying that port number/host already in used. If so, then we need to stop or send signal to process ID or process'name by using any of these this command :

     - Find process ID first
     - ps -A | grep supervisord
     - may returned process ID something like '456456'
     - then use kill command
     - kill -U SIGTERM 456456
     
               or
               
     - directly remove the process using process'name
     - pkill supervisord
  
After kill the process, need to start it again using this below command :

     supervisord // // if supervisord command didnt exist, try use /usr/local/bin/supervisord
    
# Sample Of Program

Here is the sample of program that trigger php artisan queue worker, and this worker keep running persistent even reload the web server or OSes.

    [program:test_queue] ;process's name
    command=php /home/userName/laravel/blog/artisan queue:work database --sleep=3 --daemon ;trigger artisan command
    process_name=%(program_name)s_%(process_num)02d
    user=userName
    numprocs=3    ;number of process, if 3 then 3 worker will created           
    autostart=true ;auto start the process if exit
    autorestart=true ;auto restart
    startretries=0 ;keep running
    stderr_logfile=/var/log/error_log_queue.err.log ;error log in custom place
    stdout_logfile=/var/log/output_log_queue.log ;output log in custom place
    
    ;The specified directory(logging_ specified must exist before we start the program, as Supervisor will not attempt to create any missing directories. 

# Start process

Before we can start process, make sure we notifiy supervisor the changes has been made on .conf by running this command :

    supervisorctl reread // if supervisorctl command didnt exist, try use /usr/local/bin/supervisorctl reread
    
After that, commit the changes :

    supervisorctl update // if supervisorctl command didnt exist, try use /usr/local/bin/supervisorctl update
    
Doing above command should started the process, if not, do it manually :

    supervisorctl start all // if supervisorctl command didnt exist, try use /usr/local/bin/supervisorctl start all
    
# Errors & Solutions

- http://stackoverflow.com/questions/40909842/supervisor-fatal-exited-too-quickly-process-log-may-have-details
- http://stackoverflow.com/questions/18859063/supervisor-socket-error-issue
    
    
# Reference

 - Supervisord : http://supervisord.org/
 - Step by Step : https://www.digitalocean.com/community/tutorials/how-to-install-and-manage-supervisor-on-ubuntu-and-debian-vps
