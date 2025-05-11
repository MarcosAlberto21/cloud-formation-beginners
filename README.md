
# Explicación de la plantilla CloudFormation: Infraestructura de Alta Disponibilidad con NGINX

Esta plantilla YAML despliega una arquitectura de alta disponibilidad en AWS utilizando servicios gratuitos dentro del Free Tier. A continuación se explica cada bloque de la plantilla:

---

##  Sección: `AWSTemplateFormatVersion` y `Description`
Define la versión del formato y una descripción general del propósito del stack.

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Infraestructura en alta disponibilidad con NGINX usando AWS CloudFormation
```

---

##  Sección: `Parameters`
Permite ingresar el nombre del par de llaves SSH (`KeyPair`) necesario para acceder a las instancias EC2.

```yaml
Parameters:
  KeyName:
    Description: Nombre del par de llaves SSH
    Type: AWS::EC2::KeyPair::KeyName
```

---

## Sección: `Mappings`
Define un mapa de zonas de disponibilidad (AZs) para distribuir las instancias EC2 en distintas zonas.

```yaml
Mappings:
  RegionMap:
    us-east-1:
      AZ1: us-east-1a
      AZ2: us-east-1b
```

---

## Sección: `Resources`
Contiene todos los recursos que se desplegarán:

### VPC y componentes de red
- VPC (10.0.0.0/16)
- 2 Subnets públicas (10.0.1.0/24, 10.0.2.0/24)
- Internet Gateway
- Route Table con ruta a Internet
- Asociaciones de subred a la tabla de rutas

### Grupos de Seguridad
Permiten tráfico HTTP (puerto 80) y SSH (puerto 22).

### EC2 Instances
Dos instancias `t2.micro` con Amazon Linux 2, cada una con NGINX instalado automáticamente a través de `UserData`.

### Load Balancer (ELB)
Balanceador de carga clásico (CLB) que distribuye el tráfico entre las dos instancias EC2.

---

##  Sección: `Outputs`
Muestra la URL pública del Load Balancer para acceder al sitio web desde el navegador.

```yaml
Outputs:
  LoadBalancerDNSName:
    Description: URL pública del Load Balancer
    Value: !GetAtt LoadBalancer.DNSName
```

---

## Consideraciones
- Las instancias deben estar en diferentes zonas de disponibilidad para lograr alta disponibilidad.
- El balanceador verifica automáticamente la salud de cada instancia.
- NGINX se instala de forma automatizada, y cada servidor sirve una página HTML simple.
- El diseño permite tolerancia a fallos: si una instancia falla, el tráfico se redirige a la otra.

---

##  Buenas prácticas
- Elimina la pila al terminar el laboratorio para evitar costos.
- Usa correctamente el parámetro `KeyName` con un KeyPair válido.
- Revisa el estado de salud en los Target Groups del ELB.
