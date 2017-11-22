# Process.Annotator
This is the backend/server component of process.annotator. If you are looking for the client component see [empty](empty)

### Backend/Server
The Backend consinsts of a running CouchDB instance and a *NodeJS* script that listens to CouchDB events and does some maintanance work and helps creating project databases on the clients behalve (because only admin users are allowed to do so).

If you want to use the *neonionREST*-based process.annotator (experimental) you won't need this repo, but setup the REST-API according to [empty](empty).



## Getting Started
### Prerequisites
To use *ProcessAnnotator* with your own infrastructure you first need to install following dependencies **on your server** (For testing this can be identical with your localhost/client):
- couchdb
- nodejs (>5 tested, might or might not work with lower versions)
- npm (should get installed with nodejs)

On RedHat/Fedora:
```.sh
sudo dnf install nodejs
sudo dnf install couchdb

# optionally make npm work without sudo, read and follow: https://docs.npmjs.com/getting-started/fixing-npm-permissions#option-2-change-npms-default-directory-to-another-directory
```

Please Note: In some distros, the nodejs version is quite old. I recommend using [the node version manager (nvm)](https://github.com/creationix/nvm) if you have version 4.x or older.


### Configure CouchDB
ProjectAnnotator uses a CouchDB as backend. Therefore it has to be configured first and needs to run in the background.
First edit your CouchDB configuration called `local.ini`. You can get it's location with:

`couchdb -c`

Open it in your editor of choice (you need to have write access, so don't forget eg. to `sudo`), I use vim:

`vim /usr/local/etc/couchdb/local.ini`

Then edit your `local.ini` to match the following:
```
public_fields = surname, prename, color
allow_persistent_cookies = true
users_db_public = true
```


If you installed the dependencies you should be able to (on distros using systemd, such as Fedora, Debian, ..):

```.sh
sudo systemctl enable couchdb
sudo systemctl start couchdb

npm install -g add-cors-to-couchdb
add-cors-to-couchdb
# route port 80 to 5984 (optional)
# This works on Fedora, use iptables on debian for example
sudo firewall-cmd --zone=FedoraServer --add-masquerade --permanent
sudo firewall-cmd --zone=FedoraServer --add-forward-port=port=80:proto=tcp:toport=5984 --permanent
```

Now, Clone the repo on your server:
```.sh
git clone git@github.com:nylki/ProjectAnnotator.git
cd ProjectAnnotator/server
# Install dependencies
npm install
# you can start the server with npm start
# but the optimal was is to install the systemd unit so it runs each reboot and restarts when crashed
sudo cp ../systemd/system/processannotator.service /etc/systemd/system/processannotator.service
```

Now you are all set and can try to run the client component of process.annotator to see if you get a connection running!
