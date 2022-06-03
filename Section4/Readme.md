## Setting up the Cluster in AWS-EKS
Create the cluster using EKSCTL
```
cp Section4/cluster.yaml ./
eksctl create cluster -f cluster.yaml
eksctl utils associate-iam-oidc-provider --region us-east-1 --cluster airflow --approve
```
## Creating Postgres Database
Create RDS using Console
* Same VPC
* Same Security Group
* Set DB Identifier
* Set Username/Password
* Set DB name
* Set Region
Verify you get the endpoint 
```
aws rds describe-db-instances --db-instance-identifier airflow-db | jq -r '.DBInstances[0].Endpoint.Address'
```
## Using ParamStore
Now lets modify our script to use AWS SSM Paramstore
```
aws ssm put-parameter --name "/global/airflow/webserver/flask-secret-key" --value "1cdd4a48f6586831ac8d5921693d5ec0" --type "SecureString"
```
```
aws ssm get-parameter --name "parameter-name" --with-decryption --query Parameter.Value --output text
```
Some Parameters to be Stored
* Webserver Key     - `"/global/airflow/webserver/flask-secret-key"`
* Fernet Key        - `"/global/airflow/fernet-key"`
* Git KnownHosts    - `"/global/airflow/github/known-hosts"`
* Git Private Key   - `"/global/airflow/github/private-key"`
* DAG Repo URL      - `"/global/airflow/github/dags-repo"`
* DB Identifier     - `"/dev/airflow/rds/identifier"`
* DB Password       - `"/dev/airflow/rds/password"`
>```
># How to set multi line values like Privatekey, KnownHosts etc.
>
>VAL=$(cat helperChart/secrets/known_hosts)
>
>aws ssm put-parameter --name "/global/airflow/github/known-hosts" --value "$VAL" --type "SecureString"
## Modify the Scripts or Copy from below
```
cp Section4/scripts ./ -r -f
```