# gitlab

## gitlab 安装

- 参照[https://about.gitlab.com/installation/](https://about.gitlab.com/installation/)
- 修改 external_url ，解决 8080 端口冲突：

  ```
  external_url 'http://172.18.18.40:10000'
  unicorn['listen'] = '127.0.0.1'
  unicorn['port'] = 10080
  ```

- 运行
  ```
  gitlab-ctl reconfigure
  gitlab-ctl start
  ```

## webhook and gitlab api

TODO : 通过 webhook 实现，根据 comment 自动合并 merge request.

## 参考链接
- [gitlab 8.13 80 8080端口冲突问题](blog.csdn.net/vbaspdelphi/article/details/52979836)
- [gitlab weebhook](https://docs.gitlab.com/ee/user/project/integrations/webhooks.html)
- [gitlab api](http://docs.gitlab.com/ee/api/README.html)
