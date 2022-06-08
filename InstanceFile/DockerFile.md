common 镜像

```dockerfile
FROM python:3.6.6
ARG config
ENV target /root/api
ENV TZ Asia/Shanghai

COPY ./requirements.txt /tmp/
RUN pip install --no-cache-dir --trusted-host mirrors.aliyun.com -i http://mirrors.aliyun.com/pypi/simple/ -r /tmp/requirements.txt
RUN rm /tmp/requirements.txt
# COPY ./config/__init__.py ./config/db_conf.py ./config/redis_conf.py ./config/url.py ./config/${config}.ini ${target}/config/
# COPY ./aai/ ${target}/aai/
# COPY ./const/ ${target}/const/
# COPY ./cos/ ${target}/cos/
# COPY ./handlers/ ${target}/handlers/
# COPY ./managers/ ${target}/managers/
# COPY ./orm/ ${target}/orm/
# COPY ./util/ ${target}/util/
# COPY app.py ${target}/app.py
COPY ./ ${target}/

WORKDIR ${target}
CMD python app.py ${config} --port 8890
```



