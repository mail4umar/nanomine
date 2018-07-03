# NanoMine
NanoMine Nanocomposites Data Resource

```
Copied and modified version of Rui's NanomineViz instructions from raymondino/NanomineViz
```

# Installation (REQUIRES ubuntu 16.04 -- 18.04 will not work yet)
### Note: if installing on a virtual machine, be sure to allocate at least 8G Memory, 2 CPUs and 50G Disk
- install [whyis](http://tetherless-world.github.io/whyis/install) using this command (bluedevil-oit version contains proxy work-around)
  ```
  bash < <(curl -skL https://raw.githubusercontent.com/bluedevil-oit/whyis/release/install.sh)
  ```
- whyis will be installed in /apps/whyis
- Steps to install NanoMine:
  ```
  cd /apps
  sudo git clone https://github.com/duke-matsci/nanomine.git
  sudo mkdir -p /apps/nanomine/data 2>/dev/null
  cd /apps/nanomine/data
  sudo wget https://raw.githubusercontent.com/duke-matsci/nanomine-ontology/master/xml_ingest.setl.ttl
  sudo wget https://raw.githubusercontent.com/duke-matsci/nanomine-ontology/master/ontology.setl.ttl
  sudo chown -R whyis:whyis /apps/nanomine
  sudo su - whyis
  #install n - the nodejs version manager
  
  #NOTE: requires pressing 'Y'
  # Also, the path setup for n is placed into .bashrc
  # Since whyis is not a login user i.e. we sudo su - whyis
  # the path needs to be set up in .bash_profile.
  # NEEDS TO BE FIXED or after logging into whyis next time
  #   the node build will not work.
  curl -L https://git.io/n-install | bash
  
  
  #make sure n is in the path
  source ./.bashrc
  #install/activate latest LTS NodeJS version
  n lts
  npm i -g vue-cli@2.9.6  
  cd /apps/nanomine
  npm install
  # build the vue application
  npm run build
  pip install -e .
  exit
  sudo service apache2 restart
  sudo service celeryd restart
  sudo su - whyis
  cd /apps/whyis
  python manage.py createuser -e (email) -p (password) -f (frstname) -l (lastname) -u (username) --roles=admin
  python manage.py load -i /apps/nanomine/nm.ttl -f turtle
  cd /apps
  touch .netrc
  ```
#### OK to skip the .netrc edit and load phases at least for now...
- after you create the .netrc file under /apps, edit the file and add the following.

  ```
  machine some_url (like 000.000.000.000) NO PORT -- this is the address of the nanomine MDCS server
  login some_username (like testuser1)
  password some_password (like testpwd1)
  ```
- after you edit the .netrc file, in your terminal type:
  - the xml_ingest.setl.ttl file references the nanomine server protocol, address and port and may need to  be edited
  ```
  cd /apps/whyis
  python manage.py load -i /apps/nanomine/data/ontology.setl.ttl -f turtle
  python manage.py load -i /apps/nanomine/data/xml_ingest.setl.ttl -f turtle
  ```
  - The load process can be monitored with 'sudo tail -f /var/log/celery/w1.log'
  
#### Use the server...  
- go to http://localhost/ to login with your credentials during "createuser" command
- go to http://localhost/nm to access NanoMine

# Development mode
Each time a change is made for NanoMine, apache2 and celeryd service have to be restarted manually. 
It is possible to use Whyis' development mode to bypass the need to restart services.

Try this:  
```
sudo su - whyis
cd /app/whyis
python manage.py runserver -h 0.0.0.0
``` 
After executing the commands above NanoMine may accessed on "http://localhost:5000/nm".
Then, you only need to refresh your webpage to see your changes immediately after you make changes to NanoMine. 

After you finished the visualization changes, you can shutdown the developing mode with CTRL+c.
Then you have to restart apache2 and celeryd service by
```
sudo service apache2 restart
sudo service celeryd restart
```
After this, the updated NanoMine app will show up at "http://localhost/nm".
