# �̴� ��: Azure Cloud Shell �� Bash�� ���� ����

�� ���������� Azure Portal���� Azure Cloud Shell�� Bash�� ����ϴ� ����� ���� �ڼ��� �����մϴ�.

## Cloud Shell ����

1. Azure �������� [Azure Portal](https://portal.azure.com)�� �α����մϴ�.

1. Azure Portal�� ��� Ž�� �޴����� **Cloud Shell** �� �����մϴ�.

![Cloud Shell ������](../../Linked_Image_Files/shell-icon.png)

1. ������ �����Ͽ� ���丮�� ���� �� Microsoft Azure Files ������ ����ϴ�.

1. **���丮�� �����**�� �����մϴ�.

>:pencil: **��:** ��� ���ǿ��� Azure CLI�� �ڵ����� �����˴ϴ�.

## Bash ȯ�� ����

�� â ������ ȯ�� ��Ӵٿ **Bash** �� ǥ�õǴ��� Ȯ���մϴ�.

![Bash�� ǥ���ϴ� ȯ�� ���ñ�](../../Linked_Image_Files/env-selector.png)

## ���� ����

1. �׼��� ������ ���� ���.

    ```
    az account list
    ```

1. �⺻ ���� ���� ����:

    ```
    az account set --subscription 'my-subscription-name'
    ```
>:pencil: **��:** ������ ���� ������ ���� `/home/<user>/.azure/azureProfile.json` �� ���� ���˴ϴ�.

## ���ҽ� �׷� �����

�̱� ���ο� "MyRG"��� �� ���ҽ� �׷��� ����ϴ�.

```
az group create --location westus --name MyRG
```

## Linux VM �����

�� ���ҽ� �׷쿡�� Ubuntu VM�� ����ϴ�. Azure CLI�� SSH Ű�� ����� �ش� Ű�� ����Ͽ� VM�� �����մϴ�.

```
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

>:heavy_check_mark: **����:** `--generate-ssh-keys` �� ����ϸ� VM �� `$Home` ���͸����� ���� �� �����̺� Ű�� ����� �����ϵ��� Azure CLI�� �����մϴ�. �⺻������ Ű�� `/home/<user>/.ssh/id_rsa` �� `/home/<user>/.ssh/id_rsa.pub` �� Cloud Shell�� ��ġ�˴ϴ�. `.ssh` ������ `$Home` �� �����ϴ� �� ���Ǵ� ����� ���� ������ 5GB �̹������� �����˴ϴ�.

�� VM�� ����� �̸��� Cloud Shell($User@Azure:)���� ���Ǵ� ����� �̸��Դϴ�.

## Linux VM�� ���� SSH

1. Azure Portal �˻� â���� VM �̸��� �˻��մϴ�.

1. **����**�� Ŭ���Ͽ� VM �̸��� ���� IP �ּҸ� �����ɴϴ�.

    ![���� �ɼ�](../../Linked_Image_Files/sshcmd-copy.png)

1. ssh cmd�� ����Ͽ� VM�� SSH.

    ```
    ssh username@ipaddress
    ```

SSH ������ ������ �� Ubuntu ���� ������Ʈ�� ǥ�õǾ�� �մϴ�.

![Ubuntu �λ縻 �ȳ�](../../Linked_Image_Files/ubuntu-welcome.png)

## ���� ��

1. SSH ������ �����ϴ�.

    ```
    exit
    ```

1. ���ҽ� �׷� �� �ش� �׷� ���� ��� ���ҽ��� �����մϴ�.

    ```
    az group delete -n MyRG
    ```
