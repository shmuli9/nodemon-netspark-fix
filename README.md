# nodemon-netspark-fix

Use to avoid triggering Netspark's uninstall protection when developing with nodemon
-

To get nodemon to run under Netspark with uninstall protection activated:

Browse to from your project root to node_modules/nodemon/lib/monitor/run.js

Either replace run.js with the file of the same name in this repo or:

@line 338 comment out the line 

    exec('taskkill /pid ' + child.pid + ' /T /F');

and add in (thanks to https://stackoverflow.com/a/26172779 for the send() function below): 

    var send = function (commands) {
       var self = this;
       var results = [];
       var spawn = childProcess.spawn;
       var child = spawn("powershell.exe", ["-Command", "-"  
       child.stdout.on("data", function (data) {
           self.out.push(data.toString());
       });
       child.stderr.on("data", function (data) {
           self.err.push(data.toString());
       
       commands.forEach(function (cmd) {
           self.out = [];
           self.err = [];
           child.stdin.write(cmd + '\n');
           results.push({command: cmd, output: self.out, errors: self.err});
       });
       child.stdin.end();
       return results;
    }
    
    send(['Stop-Process -ID ' + child.pid + ' -Force']);

Save, restart nodemon, and happy hacking!
