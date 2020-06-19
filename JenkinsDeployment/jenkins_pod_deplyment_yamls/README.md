<hr/>
<h3 style = 'color: green;'>Let's get started with the installation</h3>
<hr/>
1.First create a namespace (ignore jenkins.namespace.yaml if you choose this way of creating the namespace)<br/>
<code>#kubectl create namespace jenkins</code><br/>
<br/>
2. Now apply the files into the namespace 'jenkins' at once.<br/>
<code>#kubectl --namespace jenkins apply -f jenkins_pod_deplyment_yamls/</code><br/>
<br/>
3. Get the initial password from the jenkins pod logs<br/>
<code>#kubectl --namespace jenkins logs jenkins_pod_name </code><br/>(get the pod name # kubectl get pods -n jenkins)
<br/>
4. Add a port forwarding to this pod<br/>
<code>#kubectl -n jenkins port-forward --address 0.0.0.0 pod/jenkins_pod_name <any free port>:8080</code><br/>
example:<br/>
 <code>kubectl -n jenkins port-forward --address 0.0.0.0 pod/jenkins-7c4d5b787c-cnk48 9090:8080</code><br/>
 <br/>
This must keep running in order to continue with web ui of jenkins pod.<br/>
<br/><br/>


Known Issue:<br/>
<b>java.net.UnknownHostException: updates.jenkins.io</b><br/>
        at java.net.AbstractPlainSocketImpl.connect(AbstractPlainSocketImpl.java:184)<br/>
        at java.net.SocksSocketImpl.connect(SocksSocketImpl.java:392)<br/>
        at java.net.Socket.connect(Socket.java:607)<br/>
        at sun.security.ssl.SSLSocketImpl.connect(SSLSocketImpl.java:666)<br/>
        at sun.net.NetworkClient.doConnect(NetworkClient.java:175)<br/>
        at sun.net.www.http.HttpClient.openServer(HttpClient.java:463)<br/>
        at sun.net.www.http.HttpClient.openServer(HttpClient.java:558)<br/>
        at sun.net.www.protocol.https.HttpsClient.<init>(HttpsClient.java:264)<br/>
        at sun.net.www.protocol.https.HttpsClient.New(HttpsClient.java:367)<br/>
2020-06-19 08:40:02.255+0000 [id=39]    INFO    hudson.util.Retrier#start: Calling the listener of the allowed exception 'updates.jenkins.io' at the attempt #1 to do the action check updates server<br/>
2020-06-19 08:40:02.259+0000 [id=39]    INFO    hudson.util.Retrier#start: Attempted the action check updates server for 1 time(s) with no success<br/>
2020-06-19 08:40:02.261+0000 [id=39]    SEVERE  hudson.PluginManager#doCheckUpdatesServer: Error checking update sites for 1 attempt(s). Last exception was: UnknownHostException: updates.jenkins.io<br/>
2020-06-19 08:40:02.265+0000 [id=39]    INFO    hudson.model.AsyncPeriodicWork#lambda$doRun$0: Finished Download metadata. 20,244 ms<br/>
2020-06-19 08:40:02.276+0000 [id=26]    INFO    jenkins.InitReactorRunner$1#onAttained: Completed initialization<br/>
2020-06-19 08:40:02.290+0000 [id=19]    INFO    hudson.WebAppMain$3#run: Jenkins is fully up and running<br/>
<br/>
<br/>

Reason:<br/>
The nameserver for the pod does not allow the inbound traffic from updates.jenkins.io<br/>
Cause:<br/>
Need to change the service's nameserver config in order to tell kube-dns to work with google's dns server ip 8.8.8.8<br/>

<br/><br/>
This is IMP! because the plugins we need can only be installed if update site is reachable from the pod.<br/>

<hr/>

<h3 style='color:red;'>Let's clean up the mess ;)</h3>
<hr/>
<b>Objective:</b><br/>
To clean the kubernetes master n workers and just remove all of the stuffs created in above section.<br/>

Directly deleting the namespace would help but not recommended!! <br/>

Just execute the below commands:<br/>
#kubectl get pods --namespace jenkins -o wide : look at the node(worker) and remember it.
#kubectl -n jenkins delete -f jenkins_pod_deplyment_yamls/<br/>
It should drop everything which was created/applied earlier.<br/>
then you may want to remove the persistent valumes as well. Now ssh to the node you remember from get pods.<br/>
Just rm -rf to that entire directory manually :(  <br/>

<b>Yes few things are good to be done manually ;)</b>


