# **14-apache-commands-to-help-you-manage-your-webserver.**

## **Apache Installation**

Install Apache using below command.

	sudo apt update && sudo apt install apache2

## **Check Apache Version**

	apachectl -v


The output that you would get would look like this:

![Screen Shot 2021-02-25 at 5.19.36 PM.png]({{site.baseurl}}/Screen Shot 2021-02-25 at 5.19.36 PM.png)


If you change the -v with a capital -V then you would get more information like the Servern MPM and what arguments the server was compiled with.


	apachectl -V
    
 
The output that you would get would look like this:
 
![Screen Shot 2021-02-25 at 5.20.40 PM.png]({{site.baseurl}}/Screen Shot 2021-02-25 at 5.20.40 PM.png)


## **Apache Config Test**

This is one of my favorite commands is the Apache config test command as it really helps with debugging different problems with your Apache configuration:

	apachectl -t


Output in case that there are no problems with your configuration file:

![Screen Shot 2021-02-25 at 5.22.17 PM.png]({{site.baseurl}}/Screen Shot 2021-02-25 at 5.22.17 PM.png)


Output in case that you've made a mistake or if there are any issues with your Apache configuration:

![Screen Shot 2021-02-25 at 5.23.55 PM.png]({{site.baseurl}}/Screen Shot 2021-02-25 at 5.23.55 PM.png)


In the output above you can see that there is a syntax error that would prevent Apache from starting if I go ahead and try to restart the service.

## **Starting Apache**

Once you are sure that your Apache config is correct and that there are no errors, you can start the Apache service with the following systemctl command:


	sudo systemctl start apache2
    
    
## **Enabling Apache**


In order for Apache to start after a possible server reboot, you need to enable it. The exact systemctl command that you would need to use is:

	sudo systemctl enable apache2
    
That way if you reboot your server, you don't have to manually start the Apache server every time.


## **Checking Apache Status**

In order to see if the Apache service is running and how long it has been running for you can use this systemctl command:

	sudo systemctl status apache2
    
## **Restarting and reloading Apache**

If you've made any changes to your Apache service, in order for those new changes to take effect you need to reload the service:

	sudo systemctl reload apache2
    
This would not kill the Apache process and if you check your Apache status again you will be able to see that the uptime of the service is not changed.

Restarting the service on the other side, would actually stop the current process and spin up a new one:


	sudo systemctl restart apache2


If you run systemctl status apache2 right after that, you will see that the uptime is just a couple of seconds.


## **Check enabled Apache Modules**

In order to check what modules are enabled for your Apache service you can use the following command:

	apachectl -M
    
This would return a list of all enabled Apache modules.

If you wanted to check if a specific module is enabled, for example, let's say that you wanted to see if Mod HTTP2 is enabled, you could run the following command:

	apachectl -M | grep -i http2
    
If the module is enabled you would get the following output:

	http2_module (shared)
    

## **Enabling and disabling Apache modules**

Apache 2 comes with some great helper tools that let you enable and disable modules and sites.

**Enabling modules**

In order to enable a new module, you can use the following command:

	a2enmod
    
If you run the command without any arguments, you would get a list back with all available modules that you could choose from:



	Your choices are: access_compat actions alias allowmethods asis auth_basic auth_digest auth_form authn_anon authn_core authn_dbd authn_dbm authn_file authn_socache authnz_fcgi authnz_ldap authz_core authz_dbd authz_dbm authz_groupfile authz_host authz_owner authz_user autoindex brotli buffer cache cache_disk cache_socache cern_meta cgi cgid charset_lite data dav dav_fs dav_lock dbd deflate dialup dir dump_io echo env expires ext_filter file_cache filter headers hear tbeat heartmonitor http2 ident imagemap include info lbmethod_bybusyness lbmethod_byrequests lbmethod_bytraffic lbmethod_heartbeat ldap log_debug log_forensic lua macro md mime mime_magic mpm_event mpm_prefork mpm_worker negotiation proxy proxy_ajp proxy_balancer proxy_connect proxy_express proxy_fcgi proxy_fdpass proxy_ftp proxy_hcheck proxy_html proxy_http proxy_http2 proxy_scgi proxy_uwsgi proxy_wstunnel ratelimit reflector remoteip reqtimeout request rewrite sed session session_cookie session_crypto session_dbd setenvif slotmem_plain slotmem_shm socache_dbm socache_memcache socache_shmcb speling ssl status substitute suexec unique_id userdir usertrack vhost_alias xml2enc
    
    

Then you could choose a module from the list, and type it in. That way Apache would enable that specific module.


Another way to check your available modules is to list the content of the /etc/apache2/mods-available/ folder:


	ls -lah /etc/apache2/mods-available/
    
If you know the name of the module you could run the a2enmod command followed by the name of the module as an argument:

	a2enmod proxy_http2 
    
    
This would essentially create a symlink for the /etc/apache2/mods-available/proxy_http2.load file to your mods-enabled folder.

Don't forget to restart Apache after that.

**Disabling modules**

Disabling modules is pretty similar, the command that you need to use is:

	a2dismod
    
Again you would get a list of all modules and you can choose which one you would like to disable.

Similar to the a2enmod command you can add the module right after the a2dismod command as an argument to disable that specific module:


	a2dismod proxy_http2 
    

This would essentially remove the symlink that was created in the last step.

And again don't forget to restart Apache after that.


## **Enabling and disabling new virtual hosts/sites**

Apache 2 comes with some great helper tools that let you enable and disable sites. The syntax is quite similar to the a2enmod and a2dismod commands.

**Enabling sites**


In order to enable a new site, you have to first create your virtual host at:

	/etc/apache2/sites-available
    

And then you can use the following command to enable that site:

	a2ensite
    
This would essentially, create a symlink for your /etc/apache2/sites-available/site.conf config file to /etc/apache2/sites-enabled/site.conf. That way Apache will know that it has to load and enable your new site.

If you run the command without any arguments, you would get a list back with all available sites that you could choose from:


	Your choices are: 000-default-le-ssl 000-default default-ssl test-bobbyiliev-com-le-ssl test-kspl-com Which site(s) do you want to enable (wildcards ok)?
    
Then you could choose a site from the list, and type it in. That way Apache would enable that specific website/virtual host.

If you know the name of the site you could run the a2ensite command followed by the name of the site as an argument:

	a2ensite test-kspl-com
    
Don't forget to restart Apache after that.

**Disabling a website**

Disabling website is pretty similar, the command that you need to use is:


	a2dissite
    
Again you would get a list of all sites and you can choose which one you would like to disable.

Similar to the a2ensite command you can add the site right after the a2dissite command as an argument to disable that specific site:

	a2dissite test-kspl-com
    
And again don't forget to restart Apache after that.


## **Troubleshooting Apache problems**

If you are getting any errors, the first thing that you would want to do is to check your Apache error log.

By default the error log is at:

	/var/log/apache2/error.log
    
    
In order to get the last 100 entries from the log you could use the tail command:


	tail -100 /var/log/apache2/error.log
    
## **Checking Apache access logs**

More often than not you might see some spikes in your CPU, those might be a result of a brute force attack or some malicious activity against your website. To verify what kind of requests are being handled by your Apache server, you should keep an eye on your access logs.

To do so you could use again the tail command and get the last 100 entries from your access log:


	tail -100 /var/log/apache2/access.log
    














