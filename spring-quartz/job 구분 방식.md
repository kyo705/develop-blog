Spring Quartz의 Job 구분 방식
---------------------

Spring Quartz는 Spring Batch 처럼 quartz job의 상태를 DB에 저장해 진행중인 job, paused된 job 상태들을 제공해줄 뿐만 아니라 오류 발생시 재시작, 재처리에 도움을 준다.   
아래는 quartz metadata를 테이블 관계도로 나타낸 것이다.

![image](https://user-images.githubusercontent.com/89891704/197492764-ebdc8b97-606f-48f9-9415-4911ecafabb1.png)

그렇다면 quartz가 job을 실행하는 과정은 어떠할까?   

job을 실행하기 전, 해당 job의 기본 정보(jobdetail, trigger)를 데이터베이스에 저장하는데 이 때, job의 name과 group name이 같다면 quartz에서 ObjectAlreadyExistsException를 발생시킨다.
```
//LocalDataSourceJobStore.class의 storeJob 메소드
protected void storeJob(Connection conn, 
            JobDetail newJob, boolean replaceExisting)
        throws JobPersistenceException {

        boolean existingJob = jobExists(conn, newJob.getKey());
        try {
            if (existingJob) {
                if (!replaceExisting) { 
                    throw new ObjectAlreadyExistsException(newJob); 
                }
                getDelegate().updateJobDetail(conn, newJob);
            } else {
                getDelegate().insertJobDetail(conn, newJob);
            }
        } catch (IOException e) {
            throw new JobPersistenceException("Couldn't store job: "
                    + e.getMessage(), e);
        } catch (SQLException e) {
            throw new JobPersistenceException("Couldn't store job: "
                    + e.getMessage(), e);
        }
    }
```
```
//LocalDataSourceJobStore.class의 storeTrigger 메소드
protected void storeTrigger(Connection conn,
            OperableTrigger newTrigger, JobDetail job, boolean replaceExisting, String state,
            boolean forceState, boolean recovering)
        throws JobPersistenceException {

        boolean existingTrigger = triggerExists(conn, newTrigger.getKey());

        if ((existingTrigger) && (!replaceExisting)) { 
            throw new ObjectAlreadyExistsException(newTrigger); 
        }
        
        try {

            boolean shouldBepaused;

            if (!forceState) {
                shouldBepaused = getDelegate().isTriggerGroupPaused(
                        conn, newTrigger.getKey().getGroup());

                if(!shouldBepaused) {
                    shouldBepaused = getDelegate().isTriggerGroupPaused(conn,
                            ALL_GROUPS_PAUSED);

                    if (shouldBepaused) {
                        getDelegate().insertPausedTriggerGroup(conn, newTrigger.getKey().getGroup());
                    }
                }

                if (shouldBepaused && (state.equals(STATE_WAITING) || state.equals(STATE_ACQUIRED))) {
                    state = STATE_PAUSED;
                }
            }

            if(job == null) {
                job = retrieveJob(conn, newTrigger.getJobKey());
            }
            if (job == null) {
                throw new JobPersistenceException("The job ("
                        + newTrigger.getJobKey()
                        + ") referenced by the trigger does not exist.");
            }

            if (job.isConcurrentExectionDisallowed() && !recovering) { 
                state = checkBlockedState(conn, job.getKey(), state);
            }
            
            if (existingTrigger) {
                getDelegate().updateTrigger(conn, newTrigger, state, job);
            } else {
                getDelegate().insertTrigger(conn, newTrigger, state, job);
            }
        } catch (Exception e) {
            throw new JobPersistenceException("Couldn't store trigger '" + newTrigger.getKey() + "' for '" 
                    + newTrigger.getJobKey() + "' job:" + e.getMessage(), e);
        }
    }
```
따라서 상황에 맞게 적절한 jobdetails와 triggers의 name과 groupname을 설정해야한다.
