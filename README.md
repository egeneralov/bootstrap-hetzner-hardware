# egeneralov.bootstrap-hetzner-hardware

Bootstrap hardware with clean Debian. Tested on htz hw (ax-51, ex-51).

## Requirements

- `pip install netaddr`
- ansible >= 2.7

## Example

All role variables described in [defaults/main.yml](defaults/main.yml). Some examples below.

### /etc/ansible/hosts.ini

For example, let's use two groups: office - your hw server (already booted to any debian-based OS 2012+), and two servers on Hetzner.

```ini
[bootstrap]
srv-01 ansible_host=1.1.1.1
srv-02 ansible_host=2.2.2.2

[office]
lo-01 ansible_host=10.0.0.10
```


### Reboot hardware via robot API before bootstraping

Replace username/password from `head -c 32 /dev/urandom | base64` with real access to [robot API](https://robot.your-server.de/doc/webservice/en.html). Also, you can set `debug: true` to see API answers. Also, you can use `reboot_type: man` with realy big `reboot_timeout`.


```yaml
- hosts: bootstrap
  user: root
  gather_facts: no
  vars:
    hetzner:
      reboot: true
      reboot_type: hw
      reboot_timeout: 180
      username: "#ws+P6jj9cik"
      password: "uzw41cnKwhstxdhDhrBnhqCdi7YHFoGd3sfh9dhD"
  roles:
    - egeneralov.bootstrap-hetzner-hardware
```


### Bootstrap any HW/VM with custom disk size

By default, role appear to use two 1 Tb nvme drives. But you can use any disk partitioning.


```yaml
- hosts: office
  user: root
  gather_facts: no
  vars:
    disks:
      partition:
        /dev/sda:
          rootfs:
      mdadm: {}
      mount:
        /:
          src: /dev/sda1
          options: defaults,nodiscard
  roles:
    - egeneralov.bootstrap-hetzner-hardware
```


### Bootstrap and keep chroot

If you want to do more things before final reboot - just use `auto_{umount,reboot}: false` and use `chroot /target`.


```yaml
- hosts: bootstrap
  user: root
  gather_facts: no
  vars:
    auto_umount: false
    auto_reboot: false
  roles:
    - egeneralov.bootstrap-hetzner-hardware
```


License
-------

MIT

Author Information
------------------

Eduard Generalov <eduard@generalov.net>
