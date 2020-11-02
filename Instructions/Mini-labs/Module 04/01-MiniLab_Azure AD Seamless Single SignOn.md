# 미니 랩: Azure Active Directory Seamless Single Sign-On

 


Azure AD(Azure Active Directory) Seamless SSO(Seamless Single Sign-On)는 사용자가 회사 네트워크에 연결된 회사 데스크톱에 있을 때 자동으로 사용자를 로그인합니다. Seamless SSO는 추가 온-프레미스 구성 요소 없이도 사용자에게 클라우드 기반 애플리케이션에 쉽게 액세스할 수 있도록 합니다.


## 사전 요구 사항

이 미니 랩을 수행하려면 다음 리소스와 권한이 필요하므로 이 랩은 **강사용 데모**로 실행하기에 적합합니다.

* **Azure AD Connect 서버 설정**: 로그인 방법으로 통과 인증을 사용하는 경우 추가 필수 구성 요소 검사가 필요하지 않습니다. 로그인 방법으로 암호 해시 동기화를 사용하고 Azure AD Connect와 Azure AD 사이에 방화벽이 있는 경우 다음을 확인합니다.

	* Azure AD Connect 버전 1.1.644.0 이상을 사용합니다.
	
	* 방화벽 또는 프록시에서 DNS 허용 목록을 사용할 수 있다면 포트 443을 통한 *.msappproxy.net URL에 대한 연결을 허용 목록에 추가합니다. 


* **도메인 관리자 자격 증명 설정**: 다음과 같은 각 Active Directory 포리스트에 대한 도메인 관리자 자격 증명이 있어야 합니다.

	* Azure AD Connect를 통해 Azure AD에 동기화합니다.
	
	* Seamless SSO를 사용하도록 설정하려는 사용자를 포함합니다.

## Azure AD Connect 사용

1. [Azure AD Connect](https://docs.microsoft.com/ko-kr/azure/active-directory/hybrid/whatis-hybrid-identity)를 통해 Seamless SSO를 사용하도록 설정합니다.

	* Azure AD Connect의 새 설치를 수행하려는 경우 [사용자 지정 설치 경로](https://docs.microsoft.com/ko-kr/azure/active-directory/hybrid/how-to-connect-install-custom)를 선택합니다. **사용자 로그인** 페이지에서 **Single Sign-On 옵션 활성화**를 확인합니다.

		![Azure AD Connect: 사용자 로그인](../../Linked_Image_Files/SSO_demo_image1.png)

	* Azure AD Connect를 이미 설치한 경우 Azure AD Connect에서 **사용자 로그인 변경** 페이지를 선택한 다음, **다음**을 선택합니다.

		![Azure AD Connect: 사용자 로그인 변경](../../Linked_Image_Files/SSO_demo_image2.png)

1. **Single Sign-On 활성화** 페이지에 도착할 때까지 마법사를 계속합니다. 다음과 같은 각 Active Directory 포리스트에 대한 도메인 관리자 자격 증명을 제공합니다.

	* Azure AD Connect를 통해 Azure AD에 동기화합니다.

	* Seamless SSO를 사용하도록 설정하려는 사용자를 포함합니다.

1. 마법사를 완료하면 해당 테넌트에서 Seamless SSO를 사용할 수 있습니다.

## Seamless SSO가 사용하도록 설정되었는지 확인

아래 절차를 따라 Seamless SSO를 올바르게 사용하도록 설정했는지 확인합니다.

1. 테넌트에 대한 전역 관리자 자격 증명을 사용하여 [Azure Active Directory 관리 센터](https://aad.portal.azure.com/)에 로그인합니다.

1. 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

1. **Azure AD Connect**을 선택합니다.

1. Seamless Single Sign-on 기능이 *사용*으로 표시되는지 확인합니다.

	![Azure Portal: Azure AD Connect 창](../../Linked_Image_Files/SSO_demo_image3.png)

>중요

Seamless SSO는 각 AD 포리스트의 온-프레미스 AD(Active Directory)에서 AZUREADSSOACC라는 컴퓨터 계정을 만듭니다. AZUREADSSOACC 컴퓨터 계정은 보안상의 이유로 강력하게 보호되어야 합니다. 도메인 관리자만 컴퓨터 계정을 관리할 수 있어야 합니다. 컴퓨터 계정의 Kerberos 위임이 사용할 수 없도록 설정되어 있고 Active Directory의 다른 계정에 AZUREADSSOACC 컴퓨터 계정에 대한 위임 권한이 없는지 확인합니다. 컴퓨터 계정을 실수로도 삭제할 수 없고 도메인 관리자만 액세스할 수 있는 OU(조직 단위)에 컴퓨터 계정을 저장합니다.
