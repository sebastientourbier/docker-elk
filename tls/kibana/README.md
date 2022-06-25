There are three files in this directory:

1. This README file
2. elasticsearch-ca.pem
3. sample-kibana.yml
4. ca.crt / ca.key

## elasticsearch-ca.pem

The "elasticsearch-ca.pem" file is a PEM format X.509 Certificate for the Elasticsearch Certificate Authority.

You need to configure Kibana to trust this certificate as an issuing CA for TLS connections to your Elasticsearch cluster.
The "sample-kibana.yml" file, and the instructions below, explain how to do this.

## sample-kibana.yml

This is a sample configuration for Kibana to enable SSL for connections to Elasticsearch.
You can use this sample to update the "kibana.yml" configuration file in your Kibana config directory.

-------------------------------------------------------------------------------------------------
NOTE:
 You also need to update the URLs in your "elasticsearch.hosts" setting to use the "https" URL.
 e.g. If your kibana.yml file currently has

    elasticsearch.hosts: [ "http://localhost:9200" ]

  then you should change this to:

    elasticsearch.hosts: [ "https://localhost:9200" ]

-------------------------------------------------------------------------------------------------

The sample configuration assumes that you have copied the "elasticsearch-ca.pem" file directly into the Kibana config
directory without renaming it.

## ca.crt / ca.key

The ``ca.crt`` / ``ca.key`` files are the 2 parts of the TLS certificate in PEM format used to
  encrypt the connection to Kibana via the browser. ``ca.crt`` is the signed certificate,
  and ``ca.crt`` is the private key.

If you wish to regenerate them with `elasticsearch-certutil` tool using Docker, the procedure is as follows:

1. Go to the `tls/` folder:

    ```bash
    $ cd tls
    ```
   
2. Run `elasticsearch-certutil` with the `-pem` option:
   
   ```bash
    $ (sudo) docker run -it \
      -v ${PWD}:/usr/share/elasticsearch/tls \
      docker.elastic.co/elasticsearch/elasticsearch:8.2.3 \
      bin/elasticsearch-certutil ca \
        -pem
    ```

    which results in the following output where you will be prompted to enter the name of the output `.zip` file:
    ```output
    This tool assists you in the generation of X.509 certificates and certificate
    signing requests for use with SSL/TLS in the Elastic stack.
    
    The 'ca' mode generates a new 'certificate authority'
    This will create a new X.509 certificate and private key that can be used
    to sign certificate when running in 'cert' mode.
    
    Use the 'ca-dn' option if you wish to configure the 'distinguished name'
    of the certificate authority
    
    By default the 'ca' mode produces a single PKCS#12 output file which holds:
        * The CA certificate
        * The CA's private key
    
    If you elect to generate PEM format certificates (the -pem option), then the output will
    be a zip file containing individual files for the CA certificate and private key
    
    Please enter the desired output file [elastic-stack-ca.zip]: tls/elastic-stack-ca.zip
    ```
3. Extract the generated certificate files into `kibana/` and remove the zip file:
   
    ```bash
    $ unzip elastic-stack-ca.zip -d kibana/
    $ rm elastic-stack-ca.zip
    ```
    
    At this stage, the structure of the `kibana/` directory should be:

    ```output
    tls/kibana
    ├── ca
    │   ├── ca.crt
    │   └── ca.key
    ├── README.txt
    ├── elasticsearch-ca.pem
    └── sample-kibana.yml
    ```


