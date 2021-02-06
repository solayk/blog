---
layout: default
title: 간단한 CRUD 웹 만들기
parent: WEB, Spring Boot X React
nav_order: 2
last_modified_date: 2021-02-06
---
# 간단한 CRUD 웹 만들기
{: .no_toc }

본 글에선 Spring Boot, React, MyBatis, Maria DB를 연동해 사용자 정보를 DB에 CRUD 할 수 있는 웹을 만드는 과정을 설명합니다. 하나의 컴퓨터(로컬)에서 Spring Boot, React, MariaDB를 모두 기동하는 경우를 가정해 IP 주소를 localhost로 표기했습니다. 각자 환경에 맞춰 IP 주소를 변경하셔서 진행하시면 됩니다.

- Ctrl + F 로 단어를 검색하면 빠르게 찾을 수 있습니다. 
- 아래 목차는 왼편 삼각형을 누르면 숨겨집니다.

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


# React

## 1. 환경설정

1) React 앱에서 브라우저 라우터 Browser Router를 사용하기 위해 터미널에 아래 명령어로 react-router-dom을 다운로드한다.

```bash
yarn add react-router-dom
```

2) React 와 Spring Boot를 연결하기 위해 Axios를 설치한다.

```bash
yarn add axios
```

3) 디자인을 위해 [Material UI](https://material-ui.com/getting-started/installation/)를 설치한다. [React BootStrap](https://react-bootstrap.github.io/getting-started/introduction) 등 다른 라이브러리도 있지만, 이 글에선 Material UI를 쓴다.

```bash
yarn add @material-ui/core
yarn add @material-ui/icons
```

## 2. 리액트 코드

아래 사진과 같이 src 폴더 안에 component 폴더를 생성하고, 그 안에 basic, route, user 폴더와 각 파일을 만든다. App.js를 제외하곤 모두 새로 만든다.

![13](https://user-images.githubusercontent.com/73984112/107122709-b002cf00-68dc-11eb-9ff9-09cd18d43990.PNG)

### App.js (기존에 있는 파일)

```react
import React from 'react';
import AppRouter from './component/route/Router';
import NavBar from "./component/route/NavBar";

import Container from '@material-ui/core/Container';

function App() {
  return (
    <div>
      <NavBar />
      <Container>
        <AppRouter />
      </Container>
    </div>
  );
}

export default App;
```

### ApiService.js

```react
import axios from 'axios';

const USER_API_BASE_URL = "http://localhost:8000/users";

class ApiService {

    fetchUsers() {
        return axios.get(USER_API_BASE_URL);
    }

    fetchUserByID(userID) {
        return axios.get(USER_API_BASE_URL + '/' + userID);
    }

    deleteUser(userID) {
        return axios.delete(USER_API_BASE_URL + '/' + userID);
    }

    addUser(user) {
        return axios.post(USER_API_BASE_URL, user);
    }

    editUser(user) {
        return axios.put(USER_API_BASE_URL + '/' + user.id, user)
    }

}

export default new ApiService();
```

### Router.js

```react
import React from 'react';
import { BrowserRouter, Route, Switch } from 'react-router-dom';
import UserList from "../user/UserList";
import AddUser from "../user/AddUser";
import EditUser from "../user/EditUser";

const AppRouter = () => {
    return (
        <div style={style}>
            <BrowserRouter>
                <Switch>
                    <Route exact path="/" component={UserList} />
                    <Route path="/users" component={UserList} />
                    <Route path="/add-user" component={AddUser} />
                    <Route path="/edit-user" component={EditUser} />
                </Switch>
            </BrowserRouter>
        </div>
    );
}

const style = {
    marginTop: '20px'
}

export default AppRouter;
```

### NavBar.js

```react
import React from 'react';

import AppBar from '@material-ui/core/AppBar';
import Toolbar from '@material-ui/core/Toolbar';
import Typography from '@material-ui/core/Typography';
import IconButton from '@material-ui/core/IconButton';
import MenuIcon from '@material-ui/icons/Menu';

const NavBar = () => {
  return(
    <div>
      <AppBar position="static" style={style}>
        <Toolbar>
          <IconButton edge="start" color="inherit" aria-label="Menu">
            <MenuIcon />
          </IconButton>
          <Typography variant="h6" style={style}>
            TEST
          </Typography>
        </Toolbar>
      </AppBar>
    </div>
  );
}

const style = {
  flexGrow: 1,
  background : '#FDF6E3',
  color: 'black'
}

export default NavBar;
```

### UserList.js

```react
import React, { Component } from 'react';
import ApiService from "../../ApiService";

import Table from '@material-ui/core/Table'
import TableBody from '@material-ui/core/TableBody'
import TableCell from '@material-ui/core/TableCell'
import TableHead from '@material-ui/core/TableHead'
import TableRow from '@material-ui/core/TableRow'
import Button from '@material-ui/core/Button'
import Typography from '@material-ui/core/Typography'
import CreateIcon from '@material-ui/icons/Create'
import DeleteIcon from '@material-ui/icons/Delete'

class UserList extends Component {

  constructor(props) {
    super(props);

    this.state = {
      users: [],
      message: null
    }
  }

  componentDidMount() {
    this.reloadUserList();
  }

  reloadUserList = () => {
    ApiService.fetchUsers()
      .then(res => {
        this.setState({
          users: res.data
        })
      })
      .catch(err => {
        console.log('reloadUserList() Error!', err);
      })
  }

  deleteUser = (userID) => {
    ApiService.deleteUser(userID)
      .then(res => {
        this.setState({
          message: 'User Deleted Successfully.'
        });
        this.setState({
          users: this.state.users.filter(user =>
            user.id !== userID)
        });
      })
      .catch(err => {
        console.log('deleteUser() Error!', err);
      })
  }

  editUser = (ID) => {
    window.localStorage.setItem("userID", ID);
    this.props.history.push('/edit-user');
  }

  addUser = () => {
    window.localStorage.removeItem("userID");
    this.props.history.push('/add-user');
  }

  render() {

    return (
      <div>
        <Typography variant="h4" style={style}>User List</Typography>
        <Button variant="outlined" onClick={this.addUser}> Add User </Button>
        <Table>
          <TableHead>
            <TableRow>
              <TableCell>ID</TableCell>
              <TableCell>FistName</TableCell>
              <TableCell align="right">LastName</TableCell>
              <TableCell align="right">UserName</TableCell>
              <TableCell align="right">Age</TableCell>
              <TableCell align="right">Salary</TableCell>
              <TableCell align="right">Edit</TableCell>
              <TableCell align="right">Delete</TableCell>
            </TableRow>
          </TableHead>
          <TableBody>
            {this.state.users.map(user =>
              <TableRow key={user.id}>
                <TableCell component="th" scope="user">{user.id}</TableCell>
                <TableCell align="right">{user.firstName}</TableCell>
                <TableCell align="right">{user.lastName}</TableCell>
                <TableCell align="right">{user.username}</TableCell>
                <TableCell align="right">{user.age}</TableCell>
                <TableCell align="right">{user.salary}</TableCell>
                <TableCell align="right" onClick={() => this.editUser(user.id)}>
                  <CreateIcon />
                </TableCell>
                <TableCell align="right" onClick={() => this.deleteUser(user.id)}>
                  <DeleteIcon />
                </TableCell>
              </TableRow>
            )}
          </TableBody>
        </Table>
      </div>
    );

  }

}

const style = {
  display: 'flex',
  justifyContent: 'center'
}

export default UserList;
```

### AddUser.js

```react
import React, { Component } from 'react';
import ApiService from "../../ApiService";

import TextField from '@material-ui/core/TextField';
import Button from '@material-ui/core/Button';
import Typography from '@material-ui/core/Typography';

class AddUser extends Component {

    constructor(props) {
        super(props);

        this.state = {
            username: '',
            password: '',
            firstName: '',
            lastName: '',
            age: '',
            salary: '',
            message: null
        }

    }

    onChange = (e) => {
        this.setState({
            [e.target.name]: e.target.value
        })
    }

    saveUser = (e) => {
        e.preventDefault();

        let user = {
            username: this.state.username,
            password: this.state.password,
            firstName: this.state.firstName,
            lastName: this.state.lastName,
            age: this.state.age,
            salary: this.state.salary,
        }

        ApiService.addUser(user)
            .then(res => {
                this.setState({
                    message: user.username + '님이 성공적으로 등록되었습니다.'
                })
                console.log(this.state.message);
                this.props.history.push('/users');
            })
            .catch(err => {
                console.log('saveUser() 에러', err);
            });

    }

    render() {
        return (
            <div>
                <Typography variant="h4" style={style}>Add User</Typography>
                <form style={formContainer}>

                    <TextField type="text" placeholder="please input your username" name="username"
                        fullWidth margin="normal" value={this.state.username} onChange={this.onChange} />

                    <TextField type="password" placeholder="please input your password" name="password"
                        fullWidth margin="normal" value={this.state.password} onChange={this.onChange} />

                    <TextField placeholder="please input your first name" name="firstName"
                        fullWidth margin="normal" value={this.state.firstName} onChange={this.onChange} />

                    <TextField placeholder="please input your last name" name="lastName"
                        fullWidth margin="normal" value={this.state.lastName} onChange={this.onChange} />

                    <TextField type="number" placeholder="please input your age" name="age"
                        fullWidth margin="normal" value={this.state.age} onChange={this.onChange} />

                    <TextField type="number" placeholder="please input your salary" name="salary"
                        fullWidth margin="normal" value={this.state.salary} onChange={this.onChange} />

                    <Button variant="outlined" onClick={this.saveUser}>Save</Button>

                </form>
            </div>
        );
    }
}

const formContainer = {
    display: 'flex',
    flexFlow: 'row wrap'
}

const style = {
    display: 'flex',
    justifyContent: 'center'
}

export default AddUser;
```

### EditUser.js

```react
import React, { Component } from 'react';
import ApiService from "../../ApiService";

import TextField from '@material-ui/core/TextField';
import Button from '@material-ui/core/Button';
import Typography from '@material-ui/core/Typography';

class EditUser extends Component{

  constructor(props){
    super(props);

    this.state = {
      id: '',
      username: '',
      firstName: '',
      lastName: '',
      age: '',
      salary: '',
      message: null
    }
  }

  componentDidMount(){
    this.loadUser();
  }

  loadUser = () => {
    ApiService.fetchUserByID(window.localStorage.getItem("userID"))
      .then( res => {
        let user = res.data;
        this.setState({
          id: user.id,
          username: user.username,
          firstName: user.firstName,
          lastName: user.lastName,
          age: user.age,
          salary: user.salary
        })
      })
      .catch(err => {
        console.log('loadUser() 에러', err);
      });
  }

  onChange = (e) => {
    this.setState({
      [e.target.name] : e.target.value
    });
  }

  saveUser = (e) => {
    e.preventDefault();

    let user = {
      id: this.state.id,
      password: this.state.password,
      firstName: this.state.firstName,
      lastName: this.state.lastName,
      age: this.state.age,
      salary: this.state.salary
    }

    ApiService.editUser(user)
      .then( res => {
        this.setState({
          message : user.lastName + '님 정보가 수정되었습니다.'
        })
        this.props.history.push('/users');
      })
      .catch(err => {
        console.log('saveUser() 에러', err);
      })
  }

  render(){
    return(
      <div>
        <Typography variant="h4" style={style}>Edit User</Typography>
        <form>
            <TextField type="text" name="username" readOnly={true} 
fullWidth margin="normal" value={this.state.username} />

            <TextField placeholder="Edit your first name" name="firstName" 
fullWidth margin="normal" value={this.state.firstName} onChange={this.onChange} />

            <TextField placeholder="Edit your last name" name="lastName" 
fullWidth margin="normal" value={this.state.lastName} onChange={this.onChange} />

            <TextField type="number" placeholder="Edit your age" name="age" 
fullWidth margin="normal" value={this.state.age} onChange={this.onChange} />

            <TextField type="number" placeholder="Edit your salary" name="salary" 
fullWidth margin="normal" value={this.state.salary} onChange={this.onChange} />

          <Button variant="contained" color="primary" onClick={this.saveUser}>Save</Button>

        </form>
      </div>
    );
  }
}

const style = {
  display: 'flex',
  justifyContent: 'center'
}

export default EditUser;
```



# MariaDB

1) MariaDB에 접속 후 database 생성

```sql
create database springboot;
use springboot;
```

2) users 테이블 생성

```sql
CREATE TABLE users(
id int NOT NULL AUTO_INCREMENT,
username varchar(10),
password varchar(12) NOT NULL,
firstName varchar(10),
lastName varchar(10),
age tinyint,
salary int,
PRIMARY KEY(id)
)
DEFAULT CHARSET=utf8;
```

3) sample 데이터 입력

필자는 3개의 데이터를 입력했다. 원하는 만큼 자유롭게 추가한다.

```sql
INSERT INTO users(username, password, firstName, lastName, age, salary) VALUES("마리아", "1234", "ria", "ma", 32, 3000000);
```



# Spring Boot

## 1. 환경설정

1) application.properties에 MariaDB, Mybatis 설정 내용을 작성한다. 

Spring Boot는 기본으로 Tomcat이 내장되어 별도 서버 설치가 필요없다. 서버 포트를 설정하려면 server.port = 원하는 번호 입력으로 설정할 수 있고, 별도로 설정하지 않으면 Tomcat 기본 port인 8080으로 구동한다.

```properties
spring.datasource.driverClassName=org.mariadb.jdbc.Driver
spring.datasource.url=jdbc:mariadb://(아이피주소 또는 localhost):3306/springboot
spring.datasource.username=(MariaDB 아이디)
spring.datasource.password=(MariaDB 비밀번호)
 
mybatis.mapper-locations=classpath*:mybatis-mapper/*.xml

server.port = 8000
```

2) lombok 설치

Spring Boot 프로젝트 생성 시 dependency에 추가하더라도 추가 설치 작업이 필요하다. 필자는 1.18.16 버전이 설치되어 있으나, 버전이 다를 수 있다. 먼저 STS를 닫는다. 커맨드를 열고 아래 명령어로 이동한다. 버전을 모를 경우, 상위 경로로 이동 후 버전을 확인한다.

```bash
cd C:\Users\(사용자이름)\.m2\repository\org\projectlombok\lombok\1.18.16
```

아래 명령어로 설치를 시작한다.

```bash
java -jar lombok-1.18.16.jar
```

만일 IDEs를 못찾는다면 Specify location...를 눌러 Spring Tool Suite 4를 설치한 경로의 실행파일을 선택한다.

![1](https://user-images.githubusercontent.com/73984112/107118383-10394700-68c4-11eb-933d-dc0a40b57b27.PNG)

Install/Update를 클릭한다.

![2](https://user-images.githubusercontent.com/73984112/107118384-116a7400-68c4-11eb-987e-e701c930e87c.PNG)

설치가 완료되면 Spring Tool Suite 4 폴더에 lombok.jar가 보이고, 동일 경로의 SpringToolSuite4.ini 파일을 더블클릭해 -javaagent:D:\spring4\sts-4.9.0.RELEASE\lombok.jar가 추가된 것이 보이면 정상적으로 설치가 완료됐다.



## 2. 자바코드

아래 사진과 같이 src/main/java 와 src/main/resources 아래 패키지, 폴더, 파일을 추가한다.

단, DemoApplication.java 와 Servletinitializer.java 파일은 무시한다.

![12](https://user-images.githubusercontent.com/73984112/107122498-64035a80-68db-11eb-9304-a8b047b52d79.PNG)

### UserController.java (Class)

```java
package com.example.demo.controller;
import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
 
import com.example.demo.mapper.UserMapper;
import com.example.demo.domain.UserVO;
 
@CrossOrigin(origins = "*", maxAge = 3600)
@RestController
@RequestMapping("/users")
public class UserController {
 
    @Autowired
    UserMapper userMapper;
    
    @GetMapping
    public List<UserVO> userList(){
        System.out.println(userMapper.userList());
        System.out.println("유저 출력 성공");
        return userMapper.userList();
    }
    
    @PostMapping
    void insertUser(@RequestBody UserVO user) {
        userMapper.insertUser(user);
        System.out.println("유저 저장 성공");
    }
    
    @GetMapping("/{id}")
    public UserVO fetchUserByID(@PathVariable int id) {
        System.out.println(userMapper.fetchUserByID(id));
        UserVO fetchUser = userMapper.fetchUserByID(id);
        return fetchUser;
    }

    @PutMapping("/{id}")
    public void updateUser(@PathVariable int id, @RequestBody UserVO user) {
        
        UserVO updateUser = user;
        System.out.println("유저 업데이트 => " + updateUser);
        
        updateUser.setFirstName(user.getFirstName());
        updateUser.setLastName(user.getLastName());
        updateUser.setAge(user.getAge());
        updateUser.setSalary(user.getSalary());
        
        userMapper.updateUser(updateUser); 
    }
    
    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable int id) {
        userMapper.deleteUser(id);
        System.out.println("유저 삭제 성공");
    }
    
}
```

### UserVO.java (Class)

```java
package com.example.demo.domain;
import lombok.Data;
 
@Data
public class UserVO {
    int id;
    String username;
    String password;
    String firstName;
    String lastName;
    int age;
    int salary;
}
```

### UserMapper.java (Interface)

```java
package com.example.demo.mapper;
import java.util.List;
import org.apache.ibatis.annotations.Mapper;
import com.example.demo.domain.UserVO;
 
@Mapper
public interface UserMapper {
 
    List<UserVO> userList();
    UserVO fetchUserByID(int id);
    void updateUser(UserVO user);
    void insertUser(UserVO user);
    void deleteUser(int id);
    
}
```

### UserMapper.xml

```xml
package com.example.demo.mapper;
import java.util.List;
import org.apache.ibatis.annotations.Mapper;
import com.example.demo.domain.UserVO;
 
@Mapper
public interface UserMapper {
 
    List<UserVO> userList();
    UserVO fetchUserByID(int id);
    void updateUser(UserVO user);
    void insertUser(UserVO user);
    void deleteUser(int id);
    
}
```



# Web 실행

먼저 STS 에서 Ctrl + F11로 Spring Boot 서버를 시작한다.

그 다음 VS Code 에서 터미널에 아래 명령어를 입력한다.

```bash
yarn start
```

리액트 서버 기본 port는 3000이며, 별다른 설정을 하지 않았다면 자동으로 브라우저가 켜지고, 크롬에서 보고 싶다면 [http://localhost:3000](http://localhost:3000)에서 볼 수 있다.

아래와 같은 화면이 보이면서 내가 입력한 데이터가 보인다면 성공!

![15](https://user-images.githubusercontent.com/73984112/107123139-31f3f780-68df-11eb-9df2-f059c1c2a4a9.PNG)

ADD USER를 눌러 데이터도 추가해보고, Edit 과 Delete를 눌러 DB에 CRUD가 잘 작동하는지 확인한다.