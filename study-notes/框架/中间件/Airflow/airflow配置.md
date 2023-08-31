### 添加默认用户

1. 进入airflow-webserver容器中使用命令添加账户

   ```shell
   airflow users create \
       --username admin \
       --firstname Admin \
       --lastname User \
       --email admin@example.com \
       --role Admin \
       --password admin
   ```

2. 服务启动时挂载初始化脚本

   ```python
   from airflow import models, settings
   from airflow.contrib.auth.backends.password_auth import PasswordUser
   
   def create_default_user():
       user = PasswordUser(models.User())
       user.username = 'hy'  # 设置用户名
       user.email = 'hy@example.com'  # 设置用户邮箱
       user.password = '123'  # 设置用户密码
   
       session = settings.Session()
       session.add(user)
       session.commit()
       session.close()
   
   if __name__ == '__main__':
       create_default_user()
   ```

   ```yaml
   version: '3'
   
   services:
     webserver:
       image: apache/airflow:2.2.3
       environment:
         AIRFLOW__CORE__SQL_ALCHEMY_CONN: mysql://huyan:123@mysql-default:3306/airflow
         AIRFLOW__WEBSERVER__SECRET_KEY: "123"
       volumes:
         - ./dags:/opt/airflow/dags
         - ./logs:/opt/airflow/logs
         - ./plugins:/opt/airflow/plugins
         # - ./init_airflow_users.py:/init_airflow_users.py  # 将初始化脚本挂载到容器内部
   
       ports:
         - "8080:8080"
       command: ["bash", "-c", "airflow db init && airflow db upgrade && airflow webserver"]
       # command: ["bash", "-c", "airflow db init && airflow db upgrade && python /init_airflow_users.py && airflow webserver"] # 执行初始化脚本
   
       networks:
         - mysql8_dao-default
   
     scheduler:
       image: apache/airflow:2.2.3
       command: scheduler
       environment:
         AIRFLOW__CORE__SQL_ALCHEMY_CONN: mysql://huyan:123@mysql-default:3306/airflow
       volumes:
         - ./dags:/opt/airflow/dags
         - ./logs:/opt/airflow/logs
         - ./plugins:/opt/airflow/plugins
       networks:
         - mysql8_dao-default
   
   networks:
     mysql8_dao-default:
       external: true
   ```

   