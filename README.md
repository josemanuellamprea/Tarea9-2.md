# Tarea9-2.md

~~~
#!/bin/bash
#Autor: Joosé manuel Lamprea Demski
#Descripción: Creaación script para gestionar creación y eliminación máquinas virtuales.

# Función para crear una máquina virtual
crear_maquina_virtual() {
    echo "Creando nueva máquina virtual..."
    read -p "Nombre de la máquina virtual: " nombre
    read -p "Cantidad de RAM (en MB): " ram
    read -p "Cantidad de disco duro (en GB): " hd
    echo "Seleccione el sistema operativo (Debian, CentOS o Ubuntu): "
    select so in Debian CentOS Ubuntu; do
        case $so in
            Debian)
                imagen_iso="debian.iso"
                break
                ;;
            CentOS)
                imagen_iso="centos.iso"
                break
                ;;
            Ubuntu)
                imagen_iso="ubuntu.iso"
                break
                ;;
            *)
                echo "Opción no válida. Elija Debian, CentOS o Ubuntu."
                ;;
        esac
    done
    read -p "Número de CPUs: " cpus
    echo "Seleccione la red para la máquina virtual (default o otra): "
    select red in default otra; do
        case $red in
            default)
                red_virt="default"
                break
                ;;
            otra)
                read -p "Ingrese el nombre de la red personalizada: " red_virt
                break
                ;;
            *)
                echo "Opción no válida. Elija 'default' o 'otra'."
                ;;
        esac
    done

    # Crear la máquina virtual utilizando virsh
    echo "Creando la máquina virtual $nombre..."
    virsh create --name $nombre --memory ${ram}M --vcpus $cpus --disk path=/var/lib/libvirt/images/$nombre.qcow2,size=$hd --os-variant $so --network network=$red_virt --cdrom $imagen_iso

    echo "Máquina virtual $nombre creada exitosamente."
}

# Función para eliminar una máquina virtual
eliminar_maquina_virtual() {
    echo "Listado de máquinas virtuales:"
    virsh list --all

    read -p "Ingrese el nombre de la máquina virtual a eliminar: " nombre_vm
    virsh destroy $nombre_vm
    virsh undefine $nombre_vm
    echo "Máquina virtual $nombre_vm eliminada."
}

# Menú principal
while true; do
    clear
    echo "Menú de gestión de máquinas virtuales en KVM"
    echo "------------------------------------------"
    echo "1) Crear máquina virtual"
    echo "2) Eliminar máquina virtual"
    echo "3) Salir"
    echo -n "Elige una opción [1-3]: "
    read opcion

    case $opcion in
        1)
            crear_maquina_virtual
            echo -n "¿Deseas crear otra máquina virtual? (s/n): "
            read respuesta
            if [[ $respuesta != "s" ]]; then
                continue
            fi
            ;;
        2)
            eliminar_maquina_virtual
            echo -n "¿Deseas eliminar otra máquina virtual? (s/n): "
            read respuesta
            if [[ $respuesta != "s" ]]; then
                continue
            fi
            ;;
        3)
            echo "Saliendo..."
            exit 0
            ;;
        *)
            echo "Opción no válida, por favor elige de nuevo."
            ;;
    esac
    echo -n "Presiona cualquier tecla para continuar..."
    read -n 1
done

~~~ 
