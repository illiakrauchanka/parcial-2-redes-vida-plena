# Guion de la presentación (≈ 5 minutos)

Unas 750 palabras a ritmo tranquilo. Entre corchetes: cuánto tiempo dedicar a cada diapositiva.

---

**1. Portada** [15 s]

Buenas, soy Illia Krauchanka, del grupo 01, y les presento la propuesta de red para la Clínica Privada «Vida Plena».

**2. ¿Qué necesita la clínica?** [35 s]

La clínica tiene dos plantas, siete sectores y 66 empleados. Antes de diseñar, vimos qué necesita. Primero, la red no puede caerse: la guardia trabaja las 24 horas y sin red los médicos no ven las historias clínicas. Segundo, se manejan datos sensibles de pacientes, que protege la ley. Tercero, los médicos tienen que acceder rápido a los servidores HIS y PACS desde cualquier sector. Todo el diseño responde a estas tres necesidades.

**3. Esquema de la red** [40 s]

Este es el esquema. En la planta baja está el switch SW-PLANTA1 y en la planta clínica el SW-PLANTA2. Arriba están los dos proveedores de Internet, uno por cada router. Debajo, los dos switches de núcleo, unidos entre sí por dos enlaces. Abajo, los cuatro switches de acceso: uno por planta, uno para las cámaras y uno para el WiFi. Lo importante es que cada switch de acceso está conectado a los dos núcleos. La línea continua es el enlace activo; la punteada es el respaldo, que STP mantiene bloqueado hasta que hace falta.

**4. Redes A, B y C** [35 s]

Para la red interna usamos 10.0.0.0/16, que es una red privada de clase A. La dividimos en subredes /24, una por VLAN. Los enlaces con los proveedores son /30 de clase C, porque cada enlace solo necesita dos direcciones. La clase B no se usa: con la clase A sobra espacio. Los equipos fijos, como PC, servidores, cámaras e impresoras, tienen IP estática. Solo los clientes WiFi reciben IP por DHCP, y la entregan los dos núcleos con rangos separados.

**5. VLAN y enrutamiento** [30 s]

Tenemos 13 VLAN: una por sector, más servidores, voz, WiFi, invitados y cámaras. La IP indica la VLAN: la VLAN 40 es la red 10.0.4.0. El enrutamiento es estático, sin OSPF ni RIP, porque la red es chica y conocida. El tráfico entre VLAN lo enrutan los switches de núcleo, y para salir a Internet los routers hacen NAT.

**6. WiFi** [30 s]

Hay tres redes WiFi. VidaPlena-Staff es para médicos y enfermería: está en la VLAN 90, usa WPA2 con AES y tiene acceso a la red clínica. VidaPlena-BYOD y VidaPlena-Pacientes están en la VLAN 300 y solo salen a Internet. Así un paciente con su celular nunca llega a las historias clínicas.

**7. Telefonía IP** [30 s]

La voz va en su propia VLAN, la 80, separada de los datos. Así la llamada no se corta aunque la red esté cargada. El teléfono tiene un pequeño switch incorporado: la PC se conecta al teléfono y el teléfono al switch. Por un solo cable pasan dos VLAN: la 20 para la PC y la 80 para la voz. Por eso en ese puerto la seguridad permite dos direcciones MAC.

**8. STP** [25 s]

Como cada switch tiene dos enlaces, se formaría un bucle. Rapid PVST+ lo evita bloqueando uno de los dos. Si el enlace activo se corta, el de respaldo se activa en uno o dos segundos. El SW-CORE1 es el switch raíz y el SW-CORE2 es su respaldo.

**9. Contornos de la red** [30 s]

La red tiene tres contornos internos y uno externo. El contorno clínico incluye sectores, servidores, voz y WiFi del personal. El de invitados solo tiene acceso a Internet. El de cámaras solo se comunica con gestión y dirección. El externo es Internet, y desde ahí solo entra tráfico de respuesta. Los límites entre contornos son listas de acceso, ACL.

**10. Seguridad** [30 s]

Además de las ACL, cada puerto acepta un solo equipo: si alguien conecta un equipo ajeno, el puerto se apaga. En el WiFi, DHCP snooping bloquea servidores DHCP falsos. Los equipos se administran por SSH, no por Telnet, porque Telnet manda la contraseña sin cifrar.

**11. Alta disponibilidad** [40 s]

Por último, la alta disponibilidad, que era el requisito principal, porque la guardia funciona las 24 horas. Si cae el SW-CORE1, el SW-CORE2 toma el gateway gracias a HSRP. Si se cae el proveedor 1, el router RT-FW baja su prioridad y el RT-FW2 sale por el proveedor 2. Si se corta un cable, STP activa el de respaldo. Las PC no notan nada, porque su gateway siempre es la misma IP virtual, la .254. Muchas gracias. ¿Preguntas?
