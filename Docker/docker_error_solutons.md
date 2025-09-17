# Docker Error Solutons

<aside>
 Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/auth": dial unix /var/run/docker.sock: connect: permission denied

</aside>

```jsx
sudo chmod 666 /var/run/docker.sock
```

<aside>
 **Example 2: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.40/containers/json: dial unix /var/run/docker.sock: connect: permission denied**

</aside>

```jsx
sudo usermod -aG docker ${USER}
```

<aside>
 **containers/create: dial unix /var/run/docker.sock: connect: permission denied**

</aside>

```jsx
sudo setfacl --modify user::rw /var/run/docker.sock

```

<aside>
 **Example 5: Server: ERROR: Got permission denied while trying to connect to the Docker daemon socket**

</aside>

```jsx
sudo newgroup docker
sudo chmod 666 /var/run/docker.sock
sudo usermod -aG docker ${USER}

```

<aside>
 **Example 6: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post http://%2Fvar%2Frun%2Fdocker.sock/v1.24/auth: dial unix /var/run/docker.sock: connect: permission denied**

</aside>

```jsx
docker permission for linux ec2
```