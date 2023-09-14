이제 frontend(react)를 구성했고 backend(spring boot) 구성을 완료했다 현재 프로젝트에는 로그인이나 보안에 관한 특별한 기능이 없기 때문에 react에서 비동기 요청을 보낼 url 주소를 작성하고 리턴 받은 데이터를 화면에 띄워주면 된다

## cors 설정

react에서 요청을 보내서 받은 데이터를 사용해야 연결이 완료 된것이다 하지만 cors 오류 때문에 정보를 받아오지 못한다 이것을 해결하기위해 sping-boot controller 클래스 상단에 어노테인션을 추가해야한다

`@CrossOrigin(origins = "http://localhost:3000")`을 추가해서 리액트가 실행되는 3000포트에서 사용할 수 있도록 한다

&nbsp;

# React 코드 수정

리액트 코드 수정 필요한 부분

* Main.jsx
* Create.jsx
* Detail.jsx

* Edit.jsx

&nbsp;

## Main.jsx

먼저 axios를 사용할거라 `npm install axios`로 설치한다

``` jsx
import "./../App.css";
import { useEffect, useState } from "react";
import { Link } from "react-router-dom";
import axios from "axios";

function Main() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    async function data() {
      try {
        let data = await axios.get("http://localhost:8080/comments");
        // 데이터 전송 후 원하는 작업 수행
        setPosts(data.data);
        console.log("방명록을 불러왔습니다.");
      } catch (error) {
        console.error("실패했습니다.", error);
      }
    }
    data();
  }, []);

  return (
    <div className="App-header">
      <h1>안녕하세요 저는 *** 입니다 만나서 반갑습니다</h1>
      <h2>방명록</h2>
      {posts.map((post) => (
        <div key={post.id} className="card">
          <Link to={`/post/${post.id}`}>
            <div className="title">{post.subject}</div>
          </Link>
          <p className="writer">작성자: {post.writer}</p>
        </div>
      ))}
      <Link className="button" to="/write">
        방명록 작성
      </Link>
    </div>
  );
}

export default Main;

```

&nbsp;

## Create.jsx

``` jsx
import React, { useState } from "react";
import { Link, useNavigate } from "react-router-dom";
import axios from "axios";
import "./create.css";

const PostCreate = () => {
  const navigate = useNavigate();
  const [subject, setSubject] = useState("");
  const [content, setContent] = useState("");
  const [writer, setWriter] = useState("");

  const handleHome = () => {
    navigate("/");
  };

  const handleSubmit = async (e) => {
    e.preventDefault();

    const formData = new FormData();
    formData.append("subject", subject);
    formData.append("content", content);
    formData.append("writer", writer);

    try {
      await axios.post("http://localhost:8080/comments", formData, {
        headers: {
          "Content-Type": "application/json",
        },
      });
      alert("방명록이 성공적으로 생성되었습니다.");
      handleHome();
    } catch (error) {
      alert("메모 생성에 실패했습니다.", error);
    }
  };

  return (
    <div className="App-header">
      <h2>방명록 작성하기</h2>
      <form onSubmit={handleSubmit} className="card">
        <div className="form-group">
          <label className="title">제목</label>
          <input
            type="text"
            id="title"
            value={subject}
            onChange={(e) => setSubject(e.target.value)}
            required
          />
        </div>
        <div className="form-group">
          <label className="content">내용</label>
          <textarea
            id="content"
            value={content}
            onChange={(e) => setContent(e.target.value)}
            required
          />
        </div>
        <div className="form-group">
          <label htmlFor="writer">작성자</label>
          <input
            type="text"
            id="writer"
            value={writer}
            onChange={(e) => setWriter(e.target.value)}
            required
          />
          <button className="button" type="submit">
            완료
          </button>
        </div>
      </form>
      <button>
        <Link to={"/"}>돌아가기</Link>
      </button>
    </div>
  );
};

export default PostCreate;

```

&nbsp;

## Detail.jsx

``` jsx
import React, { useState, useEffect } from "react";
import { useParams, useNavigate } from "react-router-dom";
import axios from "axios";
import "./../App.css";

function PostDetail() {
  const { postId } = useParams();
  const navigate = useNavigate();
  const [post, setPost] = useState(null);

  // 특정 글을 가져오는 비동기 코드 필요
  useEffect(() => {
    async function data() {
      try {
        let data = await axios.get(`http://localhost:8080/comments/${postId}`);
        // 데이터 전송 후 원하는 작업 수행
        setPost(data.data);
        console.log("방명록을 불러왔습니다.");
      } catch (error) {
        console.error("실패했습니다.", error);
      }
    }
    data();
  }, [postId]);

  const handleHome = () => {
    navigate("/");
  };

  const handleEdit = () => {
    // 수정 페이지로 이동
    navigate(`/edit/${postId}`);
  };

  //삭제 코드
  const handleDelete = async () => {
    try {
      await axios.delete(`http://localhost:8080/comments/${postId}`);
      alert("방명록을 삭제했습니다.");
      navigate("/");
    } catch (error) {
      console.error("실패했습니다.", error);
    }
  };

  if (!post) {
    return <div>Loading...</div>;
  }

  return (
    <div className="App-header">
      <h1>방명록 상세페이지</h1>
      <div className="card">
        <h2 className="title">{post.subject}</h2>
        <p>{post.content}</p>
        <p className="writer">작성자: {post.writer}</p>
      </div>
      <div className="button-container">
        <p className="button" onClick={handleEdit}>
          수정
        </p>
        <p className="button" onClick={handleDelete}>
          삭제
        </p>
        <p className="button" onClick={handleHome}>
          돌아가기
        </p>
      </div>
    </div>
  );
}

export default PostDetail;

```

&nbsp;

## Edit.jsx

``` jsx
import React, { useState, useEffect } from "react";
import { useNavigate, useParams } from "react-router-dom";
import axios from "axios";
import "./edit.css";

const PostEdit = () => {
  const { postId } = useParams();
  const navigate = useNavigate();
  const [subject, setSubject] = useState("");
  const [content, setContent] = useState("");
  const [writer, setWriter] = useState("");

  // 기존 글 데이터를 불러오는 비동기 함수

  useEffect(() => {
    async function data() {
      try {
        let data = await axios.get(`http://localhost:8080/comments/${postId}`);
        // 데이터 전송 후 원하는 작업 수행
        setSubject(data.data.subject);
        setContent(data.data.content);
        setWriter(data.data.writer);
        console.log("방명록을 불러왔습니다.");
      } catch (error) {
        console.error("실패했습니다.", error);
      }
    }
    data();
  }, [postId]);

  const handleSubmit = async (e) => {
    e.preventDefault();

    const formData = new FormData();
    formData.append("subject", subject);
    formData.append("content", content);
    formData.append("writer", writer);

    try {
      await axios.put(`http://localhost:8080/comments/${postId}`, formData, {
        headers: {
          "Content-Type": "application/json",
        },
      });
      alert("방명록이 성공적으로 수정되었습니다.");
      handleCancel();
    } catch (error) {
      alert("방명록이 수정에 실패했습니다.", error);
    }
  };

  const handleCancel = () => {
    // 수정 취소 후 글 상세 페이지로 이동
    navigate(`/post/${postId}`);
  };

  return (
    <div className="App-header">
      <h2>Edit Post</h2>
      <form onSubmit={handleSubmit} className="card">
        <div className="form-group">
          <label>Title</label>
          <input
            type="text"
            value={subject}
            onChange={(e) => setSubject(e.target.value)}
            required
          />
        </div>
        <div className="form-group">
          <label>Content</label>
          <textarea
            value={content}
            onChange={(e) => setContent(e.target.value)}
            required
          ></textarea>
        </div>
        <div className="form-group">
          <label>Writer</label>
          <input
            type="text"
            value={writer}
            onChange={(e) => setWriter(e.target.value)}
            required
          />
        </div>
        <div>
          <div className="button-container">
            <button type="submit">수정 완료</button>
            <button type="button" onClick={handleCancel}>
              돌아가기
            </button>
          </div>
        </div>
      </form>
    </div>
  );
};

export default PostEdit;

```

