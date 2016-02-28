################################## Steamcache setup on Ubuntu ###############################################
# Thanks go to Dilbert at MPUK, for posting the original blog article these configs came from -				#
# https://blog.multiplay.co.uk/2014/04/lancache-dynamically-caching-game-installs-at-lans-using-nginx/		#
#																											#
# Additional thanks goes to "wolrah" for rewriting the nginx stuff to work on Ubuntu (mostly :) ) 			#
# https://github.com/wolrah/lancache																		#
#																											#																					#
# The following is a meandering write up of the entire process on getting MPUKs lancache config to work on 	#
# Ubuntu at a recent LAN I was netadmining. 																#				
#############################################################################################################

## Notes ##

- This version of the config only has the config for Steam-cache to work as that's all that was required at 
	the time, however now we know how it works, it should be an easy job to make the rest of it work. 


Requirements:

- Ubuntu server
- Nginx 1.6.something or higher
- dnsmasq
- A box with an amount of fast storage & lots of network - I used a desktop PC with a Gbit eth, and a 840 pro SSD
	but... this was a small LAN, and you have to work with what you've got in the bush... 

Steps:

1. Install Ubuntu 
2. Configure your network interfaces. 1 IP address is required per vhost. (see step 9 for vhosts)
3. apt-get install dnsmasq
4. amend the IP addresses for the domain IP overrides in the dnsmasq.conf to match the local server.
	these will need to match the relevant vhost (so Steam records need to match the IP for the Steam-cache vhost
	in nginx config - see step 9 if confuse) 
5. copy dnsmasq.conf file to /etc/dnsmasq/dnsmasq.conf
6. restart dnsmasq service
7. apt-get install nginx 
## the nginx version in the apt-get repos is probably old, so this will need updating to 1.6.something or better
## see below "Upgrading nginx"
8. copy nginx files to /etc/nginx - preserving the file structure from the downloaded files
9. Amend the IP addresses of the vhosts in /etc/nginx/vhosts/lancache-single.conf - making sure these match the 
	relevant DNS records in the dnsmasq.config 
10. Restart nginx service
11. Point DNS clients to this server (or forward your existing DNS server to this one) 
12. Congratulations... you are now lancaching

## Useful things

# Updating Nginx on Ubuntu

(at time of writing) Ubuntu doesn't apt-get an up to date version of Nginx. So after initially apt-get(ing) Nginx
do the below: 

1. Check your current version: "nginx -v" - if it's less than version 1.6.x, or you're getting an error with something
	to do with "Kqueue" - continue
2. If this is an existing nginx build would be wise to backup your existing Nginx config about now.. But i'm assuming 
	here that we're building a new one.
3. Stop the nginx service: "sudo service nginx stop"
4. Install the dependencies for Nginx: "sudo aptitude install python-software-properties"
5. Get the repos: "sudo add-apt-repository ppa:nginx/stable"
6. Update aptitude: "sudo aptitude update"
7. Upgrade aptitude: "sudo aptitude safe-upgrade"
8. Restart the nginx service: "sudo service nginx restart" 
9. Check the version: "nginx -v" - if it reads the same as step 1... you goofed. 