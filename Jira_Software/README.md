## Jira

<p align="center">
  <img src="https://raw.githubusercontent.com/zimmertr/Kubernetes-Manifests/master/Jira_Software/screenshot.png" width="800">
</p>

**Summary:**

These manifests are used to deploy an instance of *Atlassian Jira Software*. 

Approximate Deployment Time: 1-5 minutes

**Requirements:**  

1. Load Balancer integration so that the Service can expose the pods.
2. NFS Server to which Kubernetes can bind Persistent Volumes.
3. Directory structsure created on the NFS Endpoint you specify in `vars.yml`.
4. Python modules required to use the k8s [Ansible module](https://docs.ansible.com/ansible/latest/modules/k8s_module.html).    
    * pip install openshift kubernetes pyyaml 
    * If you're on MacOS, you might have to do [this](https://github.com/ansible/ansible/issues/43637#issuecomment-443495763) instead.  
5. Jira Software requires at minimum a trial [license](https://www.atlassian.com/software/jira/pricing?tab=self-managed) to operate. 

**Instructions:**  

1. Modify `vars.yml` with parameters according to your environment.
2. Create the necessary directories defined in `vars.yml` on your NFS server.
3. Execute the playbook: `ansible-playbook provision.yml`.  
4. Navigate to http://host.name:8080/ and click `I'll set it up myself`.
5. Select `My Own Database` and configure the database like so:
    * Database Type: PostgreSQL
    * Hostname: postgres
    * Port: 5432
    * Database: jira
    * Username: jira
    * Password: >Password set in vars.yml<
6. Click 'Next' and wait for the Postgres database to be configured by Jira.
    * If Jira fails to connect with Postgres, allow additional time for the Postgres container to finish starting.
    * You tail `tail` the logs for the Jira pod on Kubernetes to watch the progress.
7. From there, you can click `Next` and finish configuring the software as normal.

**TODO:**

1. Figure out a way to allow this to scale to more than one pod.
2. Create init container to enforce that Jira does not start up before Postgres is ready.

**Deletion:**  

1. You can roll back this deployment with the `delete.yml` playbook: `ansible-playbook delete.yml`.
    * Please note, this will not remove the deployed namespace because I could not be sure you didn't specify an existing namespace. I would hate to delete your `default` for example. So you must manually clean that up. `kubectl delete ns >namespace name<`