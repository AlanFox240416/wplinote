<details><summary>这一步</summary>
<p>

![Image](https://github.com/user-attachments/assets/0cea55a2-46be-4b7d-8a6e-d5428184bf01)

</p>
</details> 


- 对于已经申请了证书的服务，需要使用`https`
  - 如果使用`http`，没有启用`https`，会报**525错误**（证书问题）
  - 如果使用`http`，启用`https`，会报**520错误**（已经有了证书，却使用`http://127.0.0.1:12345`去反代）
- 没有申请证书的，需要使用`http`