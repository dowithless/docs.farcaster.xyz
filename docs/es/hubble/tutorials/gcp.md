# Ejecutar un Hubble en GCP

## Introducción

Esta es una guía paso a paso para configurar Hubble en GCP.
Normalmente toma menos de 30 minutos completar todo el proceso.

### Requisitos

- Cuenta en [GCP](https://console.cloud.google.com/)
- Cuenta en [Alchemy](https://www.alchemy.com/)

### Costos

- La configuración de GCP recomendada en este tutorial puede costar hasta $70/mes
- El uso de Alchemy debería mantenerse dentro del nivel gratuito

## Crear una VM en GCP

Abre **Google Cloud Shell** y ejecuta los siguientes comandos:

<figure><img src="/assets/google_cloud_shell.png" alt=""><figcaption><p>Haz clic en el ícono de Google Cloud Shell</p></figcaption></figure>

Ejecuta los siguientes comandos en la cloud shell:

<pre><code><strong>mkdir farcaster-hub
</strong>cd farcaster-hub
nano main.tf
</code></pre>

Ahora pega el contenido de abajo en tu main.tf \
Reemplaza "$YOUR_PROJECT_ID" con tu ID personal.

<figure><img src="/assets/gcp_project_id.png" /></figure>

Esta es la configuración de tu máquina virtual en GCP que será creada.

```
provider "google" {
  project = "$YOUR_PROJECT_ID"
  region  = "us-central1"
}

resource "google_compute_instance" "farcaster-hub-vm" {
  name         = "farcaster-hub-vm"
  machine_type = "e2-standard-4"  # 4 vCPUs, 16 GB de memoria
  zone         = "us-central1-a"  # Especifica la zona aquí


  boot_disk {
    initialize_params {
      image = "ubuntu-2004-focal-v20231213"  # URL de la imagen Ubuntu 20.04 LTS
      size = 160  # Tamaño de disco de 160 GB
    }
  }

  network_interface {
    network = "default"
    access_config {
      // Esto asignará una dirección IP pública a la instancia
    }
  }

  tags = ["allow-farcaster-p2p-ports"]  # Para la regla de firewall

  metadata = {
    # Puedes agregar cualquier metadato adicional aquí si es necesario
  }
}

resource "google_compute_firewall" "farcaster-p2p-ports" {
  name    = "farcaster-p2p-ports"
  network = "default"

  # permitir tráfico entrante para los puertos 2282-2285
  allow {
    protocol = "tcp"
    ports    = ["2282-2285"]
  }

  source_ranges = ["0.0.0.0/0"]
}
```

Ejecuta este comando:

```
terraform init # para inicializar terraform en tu carpeta farcaster-hub
```

Ejecuta este comando:

```
terraform plan # esto simulará tu configuración de terraform y verificará si es correcta
```

Ejemplo de salida:

<figure><img src="/assets/gcp_terraform_plan.png" alt=""><figcaption><p>Ejemplo de salida de terraform plan</p></figcaption></figure>

Habilita la API de Compute Engine

<figure><img src="/assets/gcp_compute_engine_api.png" /></figure>

Ahora ejecuta este comando:

```bash
terraform apply
```

<figure><img src="/assets/gcp_terraform_apply.png" alt=""><figcaption><p>Ejemplo de salida de Terraform apply</p></figcaption></figure>

Tomará unos minutos antes de que la VM sea creada. Tiempo para disfrutar tu :coffee:

<figure><img src="/assets/gcp_vm_overview.png" alt=""><figcaption></figcaption></figure>

Ahora deberías poder conectarte a tu VM haciendo clic en el botón **SSH**.

\
Ahora instala Docker como se describe aquí [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)

Ahora sigue los pasos descritos en la [página de instalación](../install.md) \
\
Tu Hubble estará en funcionamiento cuando veas lo siguiente :white_check_mark:

<figure><img src="/assets/gcp_hubble_running.png" alt=""><figcaption></figcaption></figure>
