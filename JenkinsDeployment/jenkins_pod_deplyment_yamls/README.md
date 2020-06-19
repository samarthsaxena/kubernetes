1.First create a namespace (ignore jenkins.namespace.yaml if you choose this way of creating the namespace)
<code>#kubectl create namespace jenkins</code>

2. Now apply the files into the namespace 'jenkins' at once.
<code>#kubectl --namespace jenkins apply -f jenkins_pod_deplyment_yamls/</code>

3. Get the initial password from the jenkins pod logs
<code>#kubectl --namespace jenkins logs <jenkins_pod_name> </code>

4. Add a port forwarding to this pod
<code>#kubectl -n jenkins port-forward --address 0.0.0.0 pod/<jenkins_pod_name> <any free port>:8080</code>
example:
 <code>kubectl -n jenkins port-forward --address 0.0.0.0 pod/jenkins-7c4d5b787c-cnk48 9090:8080</code>
 
This must keep running in order to continue with web ui of jenkins pod.



Known Issue:
<b>java.net.UnknownHostException: updates.jenkins.io</b>
        at java.net.AbstractPlainSocketImpl.connect(AbstractPlainSocketImpl.java:184)
        at java.net.SocksSocketImpl.connect(SocksSocketImpl.java:392)
        at java.net.Socket.connect(Socket.java:607)
        at sun.security.ssl.SSLSocketImpl.connect(SSLSocketImpl.java:666)
        at sun.net.NetworkClient.doConnect(NetworkClient.java:175)
        at sun.net.www.http.HttpClient.openServer(HttpClient.java:463)
        at sun.net.www.http.HttpClient.openServer(HttpClient.java:558)
        at sun.net.www.protocol.https.HttpsClient.<init>(HttpsClient.java:264)
        at sun.net.www.protocol.https.HttpsClient.New(HttpsClient.java:367)
        at sun.net.www.protocol.https.AbstractDelegateHttpsURLConnection.getNewHttpClient(AbstractDelegateHttpsURLConnection.java:191)
        at sun.net.www.protocol.http.HttpURLConnection.plainConnect0(HttpURLConnection.java:1162)
        at sun.net.www.protocol.http.HttpURLConnection.plainConnect(HttpURLConnection.java:1056)
        at sun.net.www.protocol.https.AbstractDelegateHttpsURLConnection.connect(AbstractDelegateHttpsURLConnection.java:177)
        at sun.net.www.protocol.http.HttpURLConnection.getInputStream0(HttpURLConnection.java:1570)
        at sun.net.www.protocol.http.HttpURLConnection.getInputStream(HttpURLConnection.java:1498)
        at sun.net.www.protocol.https.HttpsURLConnectionImpl.getInputStream(HttpsURLConnectionImpl.java:268)
        at hudson.model.DownloadService.loadJSON(DownloadService.java:114)
        at hudson.model.UpdateSite.updateDirectlyNow(UpdateSite.java:212)
        at hudson.model.UpdateSite.updateDirectlyNow(UpdateSite.java:207)
        at hudson.PluginManager.checkUpdatesServer(PluginManager.java:1767)
        at hudson.util.Retrier.start(Retrier.java:63)
        at hudson.PluginManager.doCheckUpdatesServer(PluginManager.java:1738)
        at jenkins.DailyCheck.execute(DailyCheck.java:93)
        at hudson.model.AsyncPeriodicWork.lambda$doRun$0(AsyncPeriodicWork.java:100)
        at java.lang.Thread.run(Thread.java:748)
2020-06-19 08:40:02.255+0000 [id=39]    INFO    hudson.util.Retrier#start: Calling the listener of the allowed exception 'updates.jenkins.io' at the attempt #1 to do the action check updates server
2020-06-19 08:40:02.259+0000 [id=39]    INFO    hudson.util.Retrier#start: Attempted the action check updates server for 1 time(s) with no success
2020-06-19 08:40:02.261+0000 [id=39]    SEVERE  hudson.PluginManager#doCheckUpdatesServer: Error checking update sites for 1 attempt(s). Last exception was: UnknownHostException: updates.jenkins.io
2020-06-19 08:40:02.265+0000 [id=39]    INFO    hudson.model.AsyncPeriodicWork#lambda$doRun$0: Finished Download metadata. 20,244 ms
2020-06-19 08:40:02.276+0000 [id=26]    INFO    jenkins.InitReactorRunner$1#onAttained: Completed initialization
2020-06-19 08:40:02.290+0000 [id=19]    INFO    hudson.WebAppMain$3#run: Jenkins is fully up and running



Reason:
The nameserver for the pod does not allow the inbound traffic from updates.jenkins.io
Cause:
Need to change the service's nameserver config in order to tell kube-dns to work with google's dns server ip 8.8.8.8


This is IMP! because the plugins we need can only be installed if update site is reachable from the pod.
