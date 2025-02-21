# Configuración de una máquina virtual Ubuntu con Docker y configuración de aplicación en angular

1. **Instalar Docker:**
   Asegúrate de tener Docker instalado en tu sistema. Puedes seguir la [documentación oficial de Docker](https://docs.docker.com/get-docker/) para instalarlo según tu sistema operativo.
2. **Descargar la imagen de Ubuntu:**
   Ejecuta el siguiente comando para descargar la imagen oficial de Ubuntu:

   ```sh
    docker pull ubuntu
   ```

3. **Crear un contenedor de Ubuntu:**
   Ejecuta el siguiente comando para crear un contenedor de Ubuntu:

   ```sh
    docker run -it --name ubuntu-container ubuntu
   ```

   - `-it`: Permite interactuar con el contenedor.
   - `--name`: Asigna un nombre al contenedor.
4. **Configurar el contenedor de Ubuntu:**
   Una vez dentro del contenedor, actualiza el sistema y configura las herramientas necesarias:

   ```sh
    apt update
    apt upgrade
    apt install vim
    apt install curl
    apt install wget
    apt install git
    apt install nodejs
    apt install npm
    apt install pm2
    apt install figlet
    npm i -g @angular/cli@16
    npm i -g typescript
   ```

5. **Clonar proyecto en angular:**
   Cambiamos al directorio donde queremos guardar el proyecto

   ```sh
      cd /home/ubuntu
   ```

   clonamos el proyecto

   ```sh
   git clone https://github.com/scr47One/banca.git
   ```

6. **Añadimos el servicio a pm2**

   Cambiamos al directorio del proyecto que clonamos

   ```sh
   cd banca
   ```

   Ejecutamos lo siguiente para añadir el servidor a pm2

   ```sh
   pm2 start "ng serve --host 0.0.0.0" --name "app"
   ```

7. Creamos un script bash en la ruta `/home/ubuntu/` llamado `init.sh` el cual se ejecutará cada que haya un nuevo bash
   y dentro añadimos este código:

   ```sh
      #!/bin/bash
      #---------------------------------
      #Script: init.sh
      #Descripci�n: Menú inicio bash
      #Uso:
      #--------------------------------

      get_status() {
      pm2_app_status=$(pm2 list | grep -E 'app.*online')
      }

      show_menu()
      {
            if [ -z "$pm2_app_status" ]; then
                     echo "1) Iniciar servicio "
            else
                     echo "1) Detener servicio "
            fi
            echo "2) Pruebas "
            echo "3) Salir "
      }

      execute_option() {
            case $1 in
                     1)
                              if [ -z "$pm2_app_status" ]; then
                                    echo "Iniciando el servicio..."
                                    pm2 start app
                              else
                                    echo "Deteniendo el servicio ..."
                                    pm2 stop app
                              fi
                              ;;
                     2)
                              npx --prefix /home/ubuntu/angular/banca ng test
                              ;;
                     3)
                              clear
                              exit 0;;
                     *) echo "Opcion invalida"
                              ;;
            esac
      }

      init() {
            local pm2_status=$(pm2 list | grep save)
            if [ -n "$pm2_status" ]; then
                     echo "Recuperando servicios..."
                     pm2 resurrect
            fi
      }

      banner() {
            figlet ANGULAR
            figlet server
      }

      init

      while true; do
            clear
            banner
            pm2 list
            get_status
            show_menu
            read -p "Elige una opcion: " option
            execute_option $option
      done
   ```

   guardamos el archivo
8. **Dar permisos de ejecución**

   Para poder ejecutar el script, debes darle permisos de ejecución. Ejecuta el siguiente comando en la terminal:

   ```sh
   chmod +x menu.sh
   ```

   Esto permitirá que el script sea ejecutable.

9. **Script ejecutable cada que se abra un nuevo bash**

   - Abrimos el archivo `~/.bashrc`
   - Agregamos al final la ruta de nuestro script `/home/ubuntu/init.sh` y listo

10. **Salir del contenedor de Ubuntu**

      Para salir del contenedor de Ubuntu, ejecuta el siguiente comando:

      ```sh
      exit
      ```

11. **Guardar los cambios en una nueva imagen:**
      Una vez que hayas realizado las configuraciones necesarias, puedes guardar los cambios en una nueva imagen de Docker. Para ello, primero detén el contenedor:

      ```sh
      docker stop ubuntu-container
      ```

      Luego, guarda los cambios en una nueva imagen:

      ```sh
      docker commit ubuntu-container ubuntu-custom
      ```

      - `ubuntu-container`: Nombre del contenedor actual.
      - `ubuntu-custom`: Nombre de la nueva imagen.
