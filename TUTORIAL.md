> The SM framework is essentially a system for bringing modularity,
> expandability, and unix variant agnosticism to shell scripting.
> It fully supports other languages, but turning shell scripting
> into a real language is its main awesomeness.
>
> This document is a tutorial walkthrough showing some nifty bits.
>
> SM requires a very recent version of zsh, which I happen to not
> have, but the authors have provided a tool that will install it in
> /opt for you, so let's start with that.

bash <( curl -k https://rvm.beginrescueend.com/install/sm-zsh )

    rpowell@ut00-s00000> bash < <( curl -k https://rvm.beginrescueend.com/install/sm-zsh )
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    101  2424  101  2424    0     0   3808      0 --:--:-- --:--:-- --:--:-- 26347
    [zsh-4.3.12] Entering Source Path /home/rpowell/.src
    [zsh-4.3.12] Downloading source tarball
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100 3618k  100 3618k    0     0   500k      0  0:00:07  0:00:07 --:--:--  672k
    [zsh-4.3.12] Extracting tarball
    [zsh-4.3.12] Entering package source
    [zsh-4.3.12] Configuring
    configuring for zsh 4.3.12
    checking build system type... x86_64-unknown-linux-gnu
    checking host system type... x86_64-unknown-linux-gnu

    [BIG SNIP]

    mkdir -p -- /opt/bin
    /usr/bin/install -c  zsh /opt/bin/zsh-4.3.12

    [BIG SNIP]

    make[1]: Entering directory `/home/rpowell/.src/zsh-4.3.12/Doc'
    /bin/sh ../mkinstalldirs /opt/share/man/man1
    for file in zsh.1 zshbuiltins.1 zshcalsys.1 zshcompctl.1 zshcompwid.1 zshcompsys.1 zshcontrib.1 zshexpn.1 zshmisc.1 zshmodules.1 zshoptions.1 zshparam.1 zshroadmap.1 zshtcpsys.1 zshzftpsys.1 zshzle.1 zshall.1; do \
                test -s ./$file || exit 1; \
                /usr/bin/install -c -m 644 ./$file /opt/share/man/man1/`echo $file | sed 's|zsh|zsh|'` || exit 1; \
            done
    make[1]: Leaving directory `/home/rpowell/.src/zsh-4.3.12/Doc'

> The next step is to install the base SM framework itself.

> First, get the new zsh, if necessary.

export PATH=/opt/bin:$PATH

> FIXME: The following bit is to get the new zsh in /bin/zsh,
> because as of 08 Dec 2011 things won't work properly otherwise.
> The devs are aware of this bug.

sudo mv /bin/zsh /bin/zsh-old    

sudo ln -fs /opt/bin/zsh /bin/zsh

> Optionally, clean out any old versions of SM you might have lying
> around.  If you install as root, this is /opt/sm instead.

rm -rf ~/.sm

> Install the latest version of SM.

curl -L https://github.com/sm/sm/tarball/master -o sm-head.tar.gz && tar zxf sm-head.tar.gz && mv sm-sm-* sm-head && cd sm-head && ./install

      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100  227k  100  227k    0     0   358k      0 --:--:-- --:--:-- --:--:-- 5172k
    S{cripting,ystem,tack} Management (SM) Framework (Core) Installation:
      [ ] Ensuring that installation target path exists (/home/rpowell/.sm)... /home/rpowell/.rvm/scripts/hook:12: array parameter hooks created globally in function
      [✔] Ensuring that installation target path exists (/home/rpowell/.sm)
      [✔] Creating install path directories (bin log tmp src)
      [✔] Installing core directories (bin)
      [✔] Installing core files (LICENSE VERSION README.md)
      [✔] Installing core extension set
      [✔] Ensuring configuration files exist (user)
      [✔] Ensuring that binaries are executable
    
    NOTE:
     Since you are installing as a user (rpowell), in order to ensure that the 'sm'  command is available to you add the following to your startup profile:
         export PATH="${PATH}:/home/rpowell/.sm/bin:/home/rpowell/.sm/pkg/active/bin:/home/rpowell/.sm/pkg/active/sbin"
     Then either close the current shell or source the profile file.
    
    S{cripting,ystem,tack} Management (SM) Framework (Core) installed.

> Clean up the download direcorty we just made in some random part of your tree without asking.

cd .. && rm -rf sm-*

> Set up the path as requested:

export PATH="${PATH}:/home/rpowell/.sm/bin:/home/rpowell/.sm/pkg/active/bin:/home/rpowell/.sm/pkg/active/sbin"

> See that we don't have any extensions.

sm ext list

    exts: #system

> FIXME: As of this writing, there's a minor bug such that if you already have rvm installed, you may get an error like this:

sm ext list                                                      

    /home/rpowell/.rvm/scripts/hook:12: array parameter hooks created globally in function
    exts: #system

> This means that your .zshrc or .zshenv or similar already uses rvm, perhaps something like this:

grep -i rvm ~/.z*                                                                                            

    /home/rpowell/.zshenv:[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm"  # This loads RVM into a shell session.

> If so, replace that line with this (you may need to run something
 like "rvm use 1.9.3 --default" to set up a default first):

    [[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/environments/default"  # This loads RVM into a shell session.

> Go into our extension directory
cd myext/

> See the layout
tree

> See that the files in the bin directory are executbale, this is what makes
> them callable from the command line.
ls -l bin/

> See that we are using 3 shell module API's
cat shell/includes

> See the code for systeminfo
cat bin/systeminfo

> Install the extension
sm ext install myext ~/testes/

> Run the systeminfo command from within the SM Framework installed extension
sm myext systeminfo

> Again look carefull, this should work on both OSX and Linux and as an extension
> developer since you are using the API you don't have to worry about irritating
> operating system differences!
cat bin/systeminfo

> Now let's look at the bin/filesystem script
cat bin/filesystem

> Install the extension.
sm ext install myext ~/testes/

> If we run this extension action now, we will see as we expect that it outputs
> the original path 'pwd' and then /etc:
sm myext filesystem

> Edit the bin/filesystem and remove '/etc' after path enter.
vim bin/filesystem

> Now install the extension again with the updates:
sm ext install myext ~/testes/

> Now when we run the extension we get a full Shell backtrace!!! :
> Not only do we get a full shell backtrace but also it gives you a more
> specific error message as to what is wrong.
sm myext filesystem

> If we copy and paste the line of the error output it brings us directly to the
> file and line in our code using whatever the defined EDITOR variable is:
vi +7   /opt/sm/exts/active/myext/bin/filesystem                            # /opt/sm/exts/active/testes/bin/filesystem

> Now we will edit the source code and add the '/etc' back on the end of the
> 'path enter' line
vim bin/filesystem

> Reinstall the extension with the fixed code:
sm ext install myext ~/testes/

> Now let's call the filesystem action again and see that it is indeed fixed:
sm myext filesystem

> Let's move on to more advanced features of the
vim bin/functioncalls

> Now we run this code:
sm myext functioncalls

> We see we have erronous output!!!
> Let's discuss various ways that we can track this error down:

> The most general thing we can do is what is called an 'all trace', this is
> similar to a full xtrace through both framework and your action code:
sm --trace=all functioncalls

> If the =* is omitted then it is equivalent to ='action' which means
> essentially: "trace my action code"
sm --trace functioncalls

> Look at bin/functioncalls to see that it is a set of 5 functions which call
> each next one. Each function sets a 'level' variable to the same number as
> the level function and then logs it to STDOUT:
cat bin/functioncalls

> In order to track down where the 'bug' is with the level variable we can do
> the following various things:
sm --trace='one' myext functioncalls
sm --trace='one ' myext functioncalls
sm --trace='three four five' myext functioncalls
sm --trace='four ' myext functioncalls

sm --var-debug=level myext functioncalls

sm --debug=main myext fucn
sm --debug=main myext functioncalls
sm --debug='main func' myext functioncalls
sm --debug='func' myext functioncalls
sm --debug myext functioncalls
sm --debug=search myext functioncalls
