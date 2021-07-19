## Gitlab 에 Jira & MatterMost 연동(Integration)
> merge, push 알림을 MM에서, 스마트 커밋으로 Jira 이슈 제어
1. MM에서 설정 > 통합 > Incomming Webhook 추가
2. gitlab에서 Integration > MM notifications > Webhook에 MM에서 생성된 URL 입력
3. Jira > Enable Jiran transitions 클릭(스마트 커밋을 위한) > Web URL에 Jira URL 등록 > username (@앞까지)> 비밀번호 
4. MM에서 /jira subscribe
    <img src="../../assets/jira subscribe.JPG">