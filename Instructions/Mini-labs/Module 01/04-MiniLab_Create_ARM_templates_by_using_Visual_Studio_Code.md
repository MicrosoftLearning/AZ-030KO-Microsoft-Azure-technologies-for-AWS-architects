# 미니 랩: Visual Studio Code를 사용하여 ARM 템플릿 만들기

이 미니 랩에서 Visual Studio Code 및 Azure Resource Manager 도구 확장을 사용하여 Azure Resource Manager 템플릿을 만들고 편집하는 방법을 알아봅니다. 확장 없이 Visual Studio Code에서 Resource Manager 템플릿을 만들 수 있지만 확장은 템플릿 개발을 단순화하는 자동 완성 옵션을 제공합니다.

[Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/) 사이트에서 사용할 수 있는 기존의 빠른 시작 템플릿 중 하나를 기반으로 ARM 템플릿을 빌드하는 것이 더 쉽고 나은 경우가 많습니다.

이 미니 랩은 [Standard Storage 계정 만들기](https://azure.microsoft.com/resources/templates/101-storage-account-create/) 템플릿을 기반으로 합니다.

## 전제 조건

필요한 사항은 다음과 같습니다.

* Visual Studio Code. 여기에 사본을 다운로드할 수 있습니다. [https://code.visualstudio.com/](https://code.visualstudio.com/).
* Resource Manager Tools 확장.

다음 단계에 따라 Resource Manager Tools 확장을 설치합니다.

1. Visual Studio Code를 엽니다.
2. **CTRL+SHIFT+X**를 눌러확장 창을 엽니다.
3. **Azure Resource Manager Tools**를 검색한다음 **설치**를 선택합니다.
4. 확장 설치를 완료하려면 **다시 로드**를 선택합니다.

## 빠른 시작 템플릿 엽니다.

1. Visual Studio Code에서 **파일 > 파일 열기** 를 선택합니다.

2. **파일 이름에** 다음 URL을 붙여넣습니다.

    ```
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. 파일을 열려면 **Open** 을 선택하십시오

4. 파일을 로컬 컴퓨터에 *Azuredeploy.json*으로 저장하려면 **파일 > 다른 이름으로 저장** 을 선택하십시오.

## 템플릿 편집

저장소 URI를 표시하려면 출력 섹션에 요소를 하나 더 추가합니다.

1. 내보낸 템플릿에 출력을 하나 더 추가합니다.

    ```json
    "storageUri": {
        "type": "string",
        "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
    },
    ```

    작업이 완료되면 출력 섹션이 다음과 같이 표시됩니다.

    ```json
    "outputs": {
        "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
        },
        "storageUri": {
            "type": "string",
            "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
        }
    }
    ```

    Visual Studio Code 내에서 코드를 복사하여 붙여 넣은 경우 **값** 요소를 다시 입력하여 Resource Manager Tools 확장의 IntelliSense 기능을 경험해 보십시오.

    ![Resource Manager 템플릿 Visual Studio Code IntelliSense](../../Linked_Image_Files/resource-manager-templates-visual-studio-code-intellisense.png)

2. 파일을 저장하려면 **파일 > 저장** 을 선택합니다.


## 템플릿 배포

템플릿을 배포하는 방법에는 여러 가지가 있으며 여기서는 Azure Cloud Shell을 사용합니다. 

1. [Azure Cloud Shell](https://shell.azure.com/)에 로그인.

2. 왼쪽 위 모서리에 있는 **PowerShell** 환경을 선택합니다. 전환할 때 셸을 다시 시작해야 합니다. 

3. **업로드/다운로드** 파일을 선택한 다음 **업로드**를 선택합니다.

    ![인터페이스에서 파일 업로드 버튼의 위치를 보여주는 이미지](../../Linked_Image_Files/azure-portal-cloud-shell-upload-file-powershell.png)

    이전 섹션에서 저장한 파일을 선택합니다. 기본 이름은 **azuredeploy.json**입니다. 셸에서 템플릿 파일에 액세스할 수 있어야 합니다. **ls** 명령과 **cat** 명령을 사용하여 파일이 성공적으로 업로드되었는지 확인할 수 있습니다.
    
4. Cloud Shell에서 다음 명령을 실행합니다. 

    ```powershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    **azuredeploy.json**이 아닌 다른 이름으로 파일을 저장하는 경우 템플릿 파일 이름을 업데이트합니다.

    다음 스크린샷은 샘플 배포를 보여 줍니다

    ![위에서 강조 표시된 변수 및 명령이 있는 Azure Portal Cloud Shell 배포 템플릿](../../Linked_Image_Files/azure-portal-cloud-shell-deploy-template-powershell.png)

    출력 섹션의 스토리지 계정 이름과 스토리지 URL이 스크린샷에 강조 표시됩니다. 

## 리소스 정리

Azure 리소스가 더 이상 필요하지 않으면 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

