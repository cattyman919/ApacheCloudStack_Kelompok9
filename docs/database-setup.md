# Installing MySQL Database in CloudStack Environment

To set up MySQL database in your CloudStack environment, follow these steps:

1.  **Update Package Repository:**

    ```bash
    sudo apt update
    ```

2.  **Install MySQL Server:**

    ```bash
    sudo apt install mysql-server
    ```

3.  **Open MySQL Configuration File:**
    Open MySQL configuration file on `/etc/mysql/mysql.conf.d/cloudstack.cnf` and make necessary changes if required depending on OS and installation location. Then, insert the following lines in the `mysqld` section:

    ```cnf
    [mysqld]
    innodb_rollback_on_timeout=1
    innodb_lock_wait_timeout=600
    max_connections=350
    log-bin=mysql-bin
    binlog-format = 'ROW'
    ```

    so, the `cloudstack.cnf` file would look like this:
    ![Cloudstack.cnf file content](https://)

4.  **Restart MySQL Services:**

    ```bash
    sudo systemctl restart mysql
    ```

5.  **Setup Database:**

    ```bash
    cloudstack-setup-databases cloud:cloud@localhost --deploy-as=root:<root password, or leave default blank if MySQL server has no root password> -i <cloudbr0 IP here>
    ```

Now you have successfully installed MySQL database in your CloudStack environment.
