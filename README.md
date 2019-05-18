# irssi-passwd
This script permits passwords to be passed to irssi on-demand from an external command (e.g. [pass](https://www.passwordstore.org/)). Keeping passwords out of config files is useful for those who wish to share their configs or those who are simply averse to storing passwords in plaintext.

This is an updated fork of the now-unmaintained project by swick: https://github.com/swick/irssi-passwd

## Installation
The passwd.pl script needs a modified version if irssi. Apply the patch irssi-connection-set-key.patch, then compile and install.

    git clone https://github.com/irssi/irssi.git
    cd irssi
    curl -O 'https://raw.githubusercontent.com/gandalf3/irssi-passwd/master/irssi-connection-set-key.patch'
    patch -p1 < irssi-connection-set-key.patch
    ./autogen.sh
    make
    sudo make install

### Archlinux
There's also an AUR package, `irssi-passwd`, which installs a patched irssi and the `passwd.pl` script.  
See the [archwiki for instructions](https://wiki.archlinux.org/index.php/Arch_User_Repository#Installing_packages)

## Usage
By default the password config is stored in `~/.irssi/passwd`, and consists of a "password_id" (`[a-zA-Z0-9_\\-]+`) and a command to print the password to stdout, separated by a colon. Each pair is separated by a newline.

    my_password       : echo "mypassword"
    my_other_password : pass show freenode

### /passwd

    /passwd password_id irc_command

will replace the string &lt;password> in irc\_command with the password received from the command associated with password\_id. irc\_command is executed at the end.

    /passwd my_password /echo <password>

The script looks up the command associated with `my_password` in the config file (`echo "mypassword"`), execute it and replace &lt;password> with the output ("mypassword"). The result (/echo "mypassword") is executed.

    /passwd my_other_password /msg NickServ identify <password>

Here the script will identify your nick with the password from pass.

### irssi server config 
If the script is loaded it will automatically replace &lt;password:password\_id> in the server config with the value received from to command associated with password\_id when the server is connecting.

    {
      address = "irc.freenode.net";
      chatnet = "Freenode";
      password = "user:<password:my_password>";
      autoconnect = "yes";
    }

Assuming the following config, irssi will automatically connect to irc.freenode.net, replace "user:&lt;password:my_password>" with "user:mypassword" and login.

