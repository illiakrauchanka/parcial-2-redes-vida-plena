CLÍNICA PRIVADA "VIDA PLENA" — CONFIGURACIONES DE RED
Grupo 01 · Comunicaciones y Redes · 2° Parcial

================================================================================
ARCHIVOS INCLUIDOS
================================================================================

Configuraciones Cisco IOS (las mismas que tiene cargadas el .pkt):

  RT-FW.cfg          Router perimetral principal 2811
                     - WAN 203.0.113.2/30 (ISP 1), LAN 10.0.10.250/24
                     - HSRP con RT-FW2 (VIP 10.0.10.249, prioridad 110, track WAN)
                     - NAT overload, ACL de entrada en WAN

  RT-FW2.cfg         Router perimetral de respaldo 2811
                     - WAN 198.51.100.2/30 (ISP 2), LAN 10.0.10.251/24
                     - HSRP prioridad 100, toma el control si cae RT-FW o su WAN

  ISP-INTERNET.cfg   Router que simula los dos proveedores e Internet
                     - Loopback0 8.8.8.8 como destino de prueba

  SW-CORE1.cfg       Switch núcleo 3560-24PS (primario)
                     - 13 SVI 10.0.x.252/24, HSRP v2 prioridad 110 (VIP 10.0.x.254)
                     - Raíz STP primaria (Rapid PVST+)
                     - Dos trunks Gi0/1-2 hacia SW-CORE2 (STP deja uno en respaldo)
                     - ACL-INVITADOS (VLAN 300) y ACL-CCTV (VLAN 400)
                     - DHCP para VLAN 90 y 300 (rango .10-.129)

  SW-CORE2.cfg       Switch núcleo 3560-24PS (respaldo)
                     - SVI 10.0.x.253/24, HSRP v2 prioridad 100
                     - Raíz STP secundaria, DHCP rango .130-.249

  SW-PLANTA1.cfg     Acceso 2960-24TT planta baja: recepción, farmacia,
                     administración, dirección, servidores, voz (Fa0/15)
  SW-PLANTA2.cfg     Acceso 2960-24TT planta clínica: guardia, consultorios,
                     laboratorio
  SW-CCTV.cfg        Acceso 2960-24TT videovigilancia (VLAN 400)
  SW-WIFI.cfg        Acceso 2960-24TT access points: Fa0/1 VLAN 90, Fa0/2-3 VLAN 300

  Todos los switches de acceso tienen doble uplink: Gi0/1 a SW-CORE1 y
  Gi0/2 a SW-CORE2.

Tablas de referencia:

  00_Direccionamiento_Hosts.txt   IP, máscara, gateway y DNS de los 37 equipos
  01_Tabla_VLAN.txt               VLAN, subred, gateway, puertos y trunks
  02_Mapa_Conexiones.txt          Los 51 enlaces físicos con puertos y cable


================================================================================
NOTAS PARA LA DEFENSA
================================================================================

1. CREDENCIALES DE LABORATORIO
   Usuario admin / contraseña VidaPlena#Adm1n (enable secret igual).
   Son de laboratorio; en producción se reemplazan y se guardan en un gestor.

2. SSH
   Las líneas VTY aceptan solo SSH v2. Las claves RSA (1024 bits) ya están
   generadas y guardadas en el .pkt para RT-FW, RT-FW2 y los 6 switches.
   Si se carga un .cfg en un equipo nuevo, generarlas con:
       crypto key generate rsa      (1024 bits)

3. HSRP SÍ FUNCIONA EN PACKET TRACER
   Verificar con "show standby brief". Prueba de failover: apagar SW-CORE1
   (o su enlace) y hacer ping continuo desde una PC a su gateway .254.

4. PoE
   El 2960-24TT del diagrama no tiene PoE. En la implementación real SW-CCTV,
   SW-WIFI y SW-PLANTA1 son 2960-24PS (o equivalentes PoE+).

5. REPRESENTACIÓN DE EQUIPOS
   Servidores, NVR, cámaras, impresoras y el teléfono IP están dibujados
   como PC-PT con su nombre, IP, máscara, gateway y VLAN reales.

6. SEGURIDAD DE PUERTOS
   Port-security en los puertos de equipos de SW-PLANTA1, SW-PLANTA2 y SW-CCTV:
   maximo 1 MAC (2 en Fa0/15 del telefono), sticky, violation shutdown.
   Un puerto en err-disabled se rehabilita con "shutdown" + "no shutdown".
   DHCP snooping en SW-WIFI (VLAN 90 y 300), trust solo en Gi0/1 y Gi0/2.

7. WIFI
   AP-STAFF, AP-BYOD y AP-PACIENTES son AccessPoint-PT-N (puentes, sin IP).
   SSID VidaPlena-Staff: WPA2-PSK/AES, clave de laboratorio VidaPlenaStaff2026.
   LAPTOP-STAFF y CEL-PACIENTE se asocian por WiFi y toman IP por DHCP.


================================================================================
CÓMO VERIFICAR DENTRO DE PACKET TRACER
================================================================================

   show running-config
   show vlan brief
   show interfaces trunk
   show spanning-tree vlan 100
   show standby brief
   show spanning-tree summary
   show ip interface brief
   show ip nat translations       (en RT-FW, después de un ping a 8.8.8.8)
   show port-security             (SW-PLANTA1/2, SW-CCTV)
   show ip dhcp snooping          (SW-WIFI)

================================================================================
Archivo principal: ../Grupo_01_VidaPlena_Parcial2.pkt
49 dispositivos · 51 enlaces cableados + 2 inalámbricos · Packet Tracer 9.0.1
