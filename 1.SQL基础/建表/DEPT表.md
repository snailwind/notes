```SQL
DROP TABLE D

-- Create table
create table DEPT
(
  deptno NUMBER(2) not null,
  dname  VARCHAR2(14),
  loc    VARCHAR2(13)
)
tablespace USERS
  pctfree 10
  initrans 1
  maxtrans 255
  storage
  (
    initial 64K
    next 1M
    minextents 1
    maxextents unlimited
  );
-- Create/Recreate primary, unique and foreign key constraints 
alter table DEPT
  add constraint PK_DEPT primary key (DEPTNO)
  using index 
  tablespace USERS
  pctfree 10
  initrans 2
  maxtrans 255
  storage
  (
    initial 64K
    next 1M
    minextents 1
    maxextents unlimited
  );
---------------------------
insert into DEPT (DEPTNO, DNAME, LOC)  
values (10, 'ACCOUNTING', 'NEW YORK');  
  
insert into DEPT (DEPTNO, DNAME, LOC)  
values (20, 'RESEARCH', 'DALLAS');  
  
insert into DEPT (DEPTNO, DNAME, LOC)  
values (30, 'SALES', 'CHICAGO');  
  
insert into DEPT (DEPTNO, DNAME, LOC)  
values (40, 'OPERATIONS', 'BOSTON');

COMMIT;
```