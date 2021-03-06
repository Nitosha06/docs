광고 파트너에 액세스 권한 부여하기
-------------------

앱 데이터에 대한 액세스 권한을 광고 파트너에게 부여하려면 먼저 광고 파트너를 Branch 계정에서 `agency`로 추가해야 합니다.  그러면 해당 광고 파트너가 여러분이 정의하는 바에 따라 앱에 대한 액세스 권한을 부여받게 됩니다.

1. **Account Settings** 로 이동하여 **Agencies** 탭을 클릭합니다.
2. **Agencies** 탭에서 **Add New Agency** 버튼을 클릭합니다.
3. **Add New Agency** 창에서 다음을 따릅니다.
   1. 드롭다운 메뉴에서 에이전시 이름을 선택합니다.
   2. 적절한 수준의 액세스 권한을 선택합니다.
      * **Admin** - 모든 설정에 대한 액세스 권한을 편집하고 모든 데이터에 대한 액세스 권한을 내보낼 수 있습니다.
      * **Team Member** - 채널 및 링크에 대한 액세스 권한, 앱 설정에 대한 읽기 전용 액세스 권한, 집계 데이터에 대한 액세스 권한을 편집할 수 있습니다.
      * **Full Read** - 모든 설정에 대한 읽기 전용 액세스 권한 및 집계 데이터에 대한 액세스 권한이 부여됩니다.
      * **Limited Read** - 집계 데이터에만 액세스할 수 있습니다.
      * **Custom** - 설정 및 데이터 액세스 권한을 사용자 지정할 수 있습니다.
      * **No Access** - 대시보드에 액세스할 수 없습니다.

   3. 'Invite'를 클릭합니다.
   4. 에이전시 계정의 모든 에이전시 관리자는 초대 이메일을 수신하게 되며, 이러한 에이전시 관리자는 누구든지 본인의 에이전시를 대표하여 초대를 수락할 수 있습니다.

!!! 경고 "에이전시에 민감한 데이터 및 앱-수준 설정 권한 부여하기"
조직 또는 앱에 대해 민감한 데이터 및 앱-수준 설정 권한이 있는 에이전시는 해당 조직/앱의 API 키에 대한 액세스 권한을 갖게 됩니다. 이 액세스 권한은 Branch의 [HTTP](https://docs.branch.io/apps/deep-linking-api/) 및 [데이터 내보내기](https://docs.branch.io/exports/api-v3/) API에 액세스하는 데 사용할 수 있습니다. 에이전시 데이터 필터(예: Only Show Agency 태그가 지정된 데이터\)는 데이터 내보내기 API를 통해 액세스된 데이터에 적용되지 않으므로, 에이전시에 이러한 권한을 부여하고 API 키를 제공하는 것은 권장하지 않습니다.

### 권한 정의하기

상기에 정의된 각 액세스 수준에는 권한이 사전 정의되어 있으며, 이는 필요에 맞게 편집할 수 있습니다.

!!! 팁 "권한 수정하기"
미리 정의된 액세스 수준을 수정하려면 연필 아이콘을 클릭하여 사용 가능한 옵션을 선택 또는 선택 해제하세요.

* **Link-level Settings** - 단일 링크의 기능에 영향을 줄 수 있는 설정 또는 기능
* **Channel-level Settings** - 마케팅 채널 전반에서 기능에 영향을 줄 수 있는 설정 또는 기능
* **App-level Settings** - 앱 전반에서 기능에 영향을 줄 수 있는 설정 또는 기능
* **Aggregate Data** - 세부적인 데이터를 포함하지 않는 요약 데이터
* **Sensitive Data** - 사용자 식별, 결제 관련, 또는 비밀 정보를 포함할 수 있는 데이터

### 기타 데이터 필터

Branch 계정에 에이전시 액세스 권한을 부여할 때는 언제든지 에이전시가 이용할 수 있는 데이터를 제한할 수도 있습니다.

!!! 경고 "특정 광고 네트워크의 데이터만 표시하도록 필터링하기"
Branch 계정에 대한 액세스 권한을 광고 파트너에게 부여할 때는 `Only Show Data from Specific Ad Networks`의 토글을 켜짐으로 전환하고 액세스 권한을 부여하고자 하는 광고 파트너를 선택해야 합니다.

* **Only Show Agency-tagged Data** - 켜짐으로 설정할 경우 에이전시 사용자는 해당 에이전시의 ID가 태그된 이벤트만 볼 수 있습니다.
* **Restrict Access to Revenue Data** - 켜짐으로 설정할 경우 에이전시 사용자가 수익 데이터를 볼 수 없습니다.
* **Only Show Data from Specific Ad Networks** - 켜짐으로 설정할 경우 에이전시 사용자가 특정 광고 네트워크 목록의 이벤트만 볼 수 있습니다.
* **Only Show Data from Specific Locations** - 켜짐으로 설정할 경우 에이전시 사용자는 특정 목록의 국가에서 발생한 이벤트만 볼 수 있습니다.

!!! 경고 "에이전시 초대"
에이전시에 적절한 수준의 액세스 권한을 정의한 뒤에는 반드시 **초대** 를 통해 에이전시가 Branch 대시보드에 액세스하도록 초대해야 합니다. 조직 관리자만 에이전시가 Branch 대시보드에 액세스하도록 초대할 수 있습니다.
