@@ -1,73 +0,0 @@
# Report for the Devops exercise

This repo builds on the original repo https://github.com/deviantony/docker-elk.

In brief:
- I have reintegrated all changes made in the `tls` branch of the original repo,
  that provides TLS encryption for Elasticsearch.
- I have regenerated all TLS certificates following the original instructions [tls/README.md](./tls/README.md).
- I generated an additional new pair of ``ca.crt/ca.key`` certificate for
  the TLS encryption of the connection to Kibana.
  Instructions for regeneration have been added to [tls/kibana/README.m](./tls/kibana/README.md).

Note that to reach this point, I worked further after the exercise and
  my local machine, and so `tls/instances.yml` gives the configuration of
  a local setup and not the setup of the exercise.
In this sense, it should be modified to accommodate the 2 DNS and IP of the HIP server.
In addition, the IP should be reviewed in the different configuration files
  (i.e `elasticsearch/config/elasticsearch.yml`, `kibana/config/kibana.yml`,
  `logstash/pipeline/logstash.conf`)

# Instructions to setup and run the Elasticsearch/Logstash/Kibana stack using TLS encryption

1.   Install docker engine and docker compose with `apt-get` following instructions
     at https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository.
     I also added $USER to the docker group.

2.   Clone repo https://github.com/sebastientourbier/docker-elk and switch to the branch `tls`

     ```bash
     $ git clone https://github.com/sebastientourbier/docker-elk.git
     $ cd docker-elk
     $ git checkout -t origin/tls
     ```
     
     Note that during the exercise I cloned the original repository.
     While the connection to elasticsearch was TLS-encrypted, the connection to Kibana was not.
     In this version, I went further and made appropriate changes to achieve this,
       including the generation of a new certificate

3.   Build and run the ELK stack

     ```
     $ docker compose up -d
     ```
     
     To check the status containers to see if all are running:
     
     ```bash
     $ cd ~/docker-elk
     $ docker-compose ps
     ```
     
     To stop the running containers:

     ```bash
     $ docker-compose down -v
     ```
     
4.   Test Elasticsearch

      4.1.  On the server

          ```bash
          $ curl https://es.test4.thehip.app:9200 -u elastic:changeme -k
          ```

          Output:
          ```output
          {
            "name" : "06b98858c7ee",
            "cluster_name" : "docker-cluster",
            "cluster_uuid" : "kZCdORVURAewvwzot9vdcw",
            "version" : {
              "number" : "8.2.3",
              "build_flavor" : "default",
              "build_type" : "docker",
              "build_hash" : "9905bfb62a3f0b044948376b4f607f70a8a151b4",
              "build_date" : "2022-06-08T22:21:36.455508792Z",
              "build_snapshot" : false,
              "lucene_version" : "9.1.0",
              "minimum_wire_compatibility_version" : "7.17.0",
              "minimum_index_compatibility_version" : "7.0.0"
            },
            "tagline" : "You Know, for Search"
          }
          ```

      4.2.  From my machine
      
      I opened https://es.test4.thehip.app:9200 in my browser and
       enter the elastic credentials (user: elastic, pwd: changeme)
      
      Note that here, I am using the credentials by default.
      To change it, please follow the instructions in the [README](README_original.md)
       of the original repository.

5.   Test Kibana in the browser

     On your machine, open https://es.test4.thehip.app:5601.
     Then you can go to ``Stack Management`` and a create a new DataView
       from the indexed data injected by Logstash
     
6.   Configure to start the stack at login by creating a `~/.bashrc`
     with the following content:

     ```bash
     # Launch the ELK stack at startup
     cd docker-elk/
     docker-compose up -d
     cd ~
     ```
