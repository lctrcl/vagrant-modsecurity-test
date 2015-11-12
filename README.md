# Modsecurity Test Environment + DVWA/Mutillidae

based on https://github.com/xpn/vagrant-modsec

This vagrant box spins up a very basic Apache HTTP server configured with modsecurity.

Note: This host is only for testing modsecurity rules.

To use:

```bash
git clone https://github.com/lctrcl/vagrant-modsecurity-test.git
cd vagrant-modsecurity-test
vagrant up
```


Login to DVWA: http://10.10.10.11/DVWA/setup.php, setup DB and login to http://10.10.10.11/DVWA/login.php ; admin:password.

Login to Mutillidae: http://10.10.10.11/mutillidae, setup DB

ModSecurity Logs: sudo tail -f /var/log/apache2/modsec_audit.log
