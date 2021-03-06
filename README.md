# grunt-sftp-deploy

This is a [grunt](https://github.com/gruntjs/grunt) task for code deployment over the _sftp_ protocol. It is mostly a copy of [grunt-ftp-deploy](https://github.com/zonak/grunt-ftp-deploy), but uses [ssh2](https://github.com/mscdex/ssh2) to provide sftp access instead of [jsftp](https://github.com/sergi/jsftp). And when I say "mostly a copy," I mean I stole it all and added sftp. Including this readme, for now.

These days _git_ is not only our goto code management tool but in many cases our deployment tool as well. But there are many cases where _git_ is not really fit for deployment:

- we deploy to servers with only _sftp_ access
- the production code is a result of a build process producing files that we do not necessarily track with _git_

This is why a _grunt_ task like this would be very useful.

For simplicity purposes this task avoids deleting any files and it is not trying to do any size or time stamp comparison. It simply transfers all the files (and folder structure) from your dev / build location to a location on your server.

## Usage

To use this task you will need to include the following configuration in your _grunt_ file:

```javascript
'sftp-deploy': {
  build: {
    auth: {
      host: 'server.com',
      port: 21,
      authKey: 'key1'
    },
    src: '/path/to/source/folder',
    dest: '/path/to/destination/folder',
    exclusions: ['/path/to/source/folder/**/.DS_Store', '/path/to/source/folder/**/Thumbs.db', 'dist/tmp'],
    server_sep: '/'
  }
}
```

and load the task:

```javascript
grunt.loadNpmTasks('grunt-sftp-deploy');
```

The parameters in our configuration are:

- **host** - the name or the IP address of the server we are deploying to
- **port** - the port that the _sftp_ service is running on
- **authKey** - a key for looking up the saved credentials
- **src** - the source location, the local folder that we are transferring to the server
- **dest** - the destination location, the folder on the server we are deploying to
- **exclusions** - an optional parameter allowing us to exclude files and folders by utilizing grunt's support for `minimatch`. Please note that the definitions should be relative to the project root
- **server_sep** - an optional parameter allowing you to define the server separator in case it differs from your local environment. Useful if you deploy from Windows to Unix

## Authentication parameters

Usernames, passwords, and private key references are stored as a JSON object in a file named `.ftppass`. This file should be omitted from source control. It uses the following format:

```javascript
{
  "key1": {
    "username": "username1",
    "password": "password1"
  },
  "key2": {
    "username": "username2",
    "password": "password2"
  },
  "privateKey": {    
    "username": "username"
  },
  "privateKeyEncrypted": {    
    "username": "username",
    "passphrase": "passphrase1"
  },
  "privateKeyCustom": {    
    "username": "username",
    "passphrase": "passphrase1",
    "keyLocation": "/full/path/to/key"
  }
}
```

If `keyLocation` is not specified, `grunt-sftp-deploy` looks for keys at `~/.ssh/id_dsa` and `/.ssh/id_rsa`.

You can supply passwords for encrypted keys with the `passphrase` attribute.

This way we can save as many username / password combinations as we want and look them up by the `authKey` value defined in the _grunt_ config file where the rest of the target parameters are defined.

## Dependencies

This task is built by taking advantage of the great work of Brian White and his [ssh2](https://github.com/mscdex/ssh2) _node.js_ module.

