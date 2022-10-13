### [과제] 숙련주차 과제 답


1

추가하기 버튼을 클릭해도 추가한 아이템이 화면에 표시되지 않음.


form 안에 추가하기 리듀서로 보내줄 dispatch가 없었음 
```

const onSubmitHandler = (event) => {
    event.preventDefault();
    if (todo.title.trim() === "" || todo.body.trim() === "") return;

    setTodo({
      id: 0,
      title: "",
      body: "",
      isDone: false,
    });
  };
const dispatch = useDispatch();
```

```
const onSubmitHandler = (event) => {
    event.preventDefault();
    if (todo.title.trim() === "" || todo.body.trim() === "") return;

    dispatch(addTodo(todo));

    setTodo({
      id: 0,
      title: "",
      body: "",
      isDone: false,
    });
  };
  ```

2

추가하기 버튼 클릭 후 기존에 존재하던 아이템들이 사라짐.
고유한 id 값이 없어서 추가해도 0번째로 같아짐

```
onSubmitHandler 로 보낼때 id 값을 고유한 id값으로 바꿔서 보내줌
  const onSubmitHandler = (event) => {
    event.preventDefault();
    if (todo.title.trim() === "" || todo.body.trim() === "") return;

    setTodo({
      id: 0,
      title: "",
      body: "",
      isDone: false,
    });
  };
```

```
  const id = nextId();
  const adddispatch = useDispatch();
  
  const onSubmitHandler = (event) => {
    event.preventDefault();
    if (todo.title.trim() === "" || todo.body.trim() === "") return;

    adddispatch(addTodo(todo));

    setTodo({
      id,
      title: "",
      body: "",
      isDone: false,
    });
  };
```

todo reducer의 case : ADD_TODO의 Todos를 변경 

```
const todos = (state = initialState, action) => {
  switch (action.type) {
    case ADD_TODO:
      return {
        ...state,
        todos: [action.payload],
      };

```

```
switch (action.type) {
    case ADD_TODO:
      return {
        ...state,
        todos: [...state.todos, action.payload],
      };
```

3

삭제 기능이 동작하지 않음.
디스패치는 있지만 리듀서에 구현이 안됨

```
const todos = (state = initialState, action) => {
  switch (action.type) {
    case ADD_TODO:
      return {
        ...state,
        todos: [...state.todos, action.payload],
      };

    case TOGGLE_STATUS_TODO:
      return {
        ...state,
        todos: state.todos.map((todo) => {
          if (todo.id === action.payload) {
            return {
              ...todo,
              isDone: !todo.isDone,
            };
          } else {
            return todo;
          }
        }),
      };

    case GET_TODO_BY_ID:
      return {
        ...state,
        todo: state.todos.find((todo) => {
          return todo.id === action.payload;
        }),
      };
    default:
      return state;
  }
};

export default todos;
```

리듀서 코드 추가 

```
case DELETE_TODO:
      return {
        ...state,
        todos: [...state.todos.filter((todos) => todos.id !== action.payload)],
      };

```
4 

상세 페이지에 진입 하였을 때 데이터가 업데이트 되지 않음.
상세보기 눌렀을때 받아오는 디스패치가 없음. 

```
const Detail = () => {
  const dispatch = useDispatch();
  const todo = useSelector((state) => state.todos.todo);

  const { id } = useParams();
  const navigate = useNavigate();

  return (
    <StContainer>
      <StDialog>
        <div>
          <StDialogHeader>
            <div>ID :{todo.id}</div>
            <StButton
              borderColor="#ddd"
              onClick={() => {
                navigate("/");
              }}
            >
              이전으로
            </StButton>
          </StDialogHeader>
          <StTitle>{todo.title}</StTitle>
          <StBody>{todo.body}</StBody>
        </div>
      </StDialog>
    </StContainer>
  );
};

export default Detail;
```

```
const Detail = () => {
  const dispatch = useDispatch();
  const todo = useSelector((state) => state.todos.todo);

  const { id } = useParams();
  const navigate = useNavigate();

  useEffect(() => {
    dispatch(getTodoByID(id));
  }, [dispatch, id]);

  return (
    <StContainer>
      <StDialog>
        <div>
          <StDialogHeader>
            <div>ID :{todo.id}</div>
            <StButton
              borderColor="#ddd"
              onClick={() => {
                navigate("/");
              }}
            >
              이전으로
            </StButton>
          </StDialogHeader>
          <StTitle>{todo.title}</StTitle>
          <StBody>{todo.body}</StBody>
        </div>
      </StDialog>
    </StContainer>
  );
};

export default Detail;
```

5

완료된 카드의 상세 페이지에 진입 하였을 때 올바른 데이터를 불러오지 못함.
Done..! 리스트 안에 상세보기에 연결된 Link에 보내는 값이 인덱스가 아닌 todo.id가 되야함

```
<h2 className="list-title">Done..! 🎉</h2>
      <StListWrapper>
        {todos.map((todo, index) => {
          if (todo.isDone) {
            return (
              <StTodoContainer key={todo.id}>
                <StLink to={`/${index}`} key={todo.id}>
                  <div>상세보기</div>
                </StLink>
                <div>
                  <h2 className="todo-title">{todo.title}</h2>
                  <div>{todo.body}</div>
                </div>
                <StDialogFooter>
                  <StButton
                    borderColor="red"
                    onClick={() => onDeleteTodo(todo.id)}
                  >
                    삭제하기
                  </StButton>
                  <StButton
                    borderColor="green"
                    onClick={() => onToggleStatusTodo(todo.id)}
                  >
                    {todo.isDone ? "취소!" : "완료!"}
                  </StButton>
                </StDialogFooter>
              </StTodoContainer>
            );
          } else {
            return null;
          }
        })}
      </StListWrapper>
```

```
<h2 className="list-title">Done..! 🎉</h2>
      <StListWrapper>
        {todos.map((todo, index) => {
          if (todo.isDone) {
            return (
              <StTodoContainer key={todo.id}>
                <StLink to={`/${todo.id}`} key={todo.id}>
                  <div>상세보기</div>
                </StLink>
                <div>
                  <h2 className="todo-title">{todo.title}</h2>
                  <div>{todo.body}</div>
                </div>
                <StDialogFooter>
                  <StButton
                    borderColor="red"
                    onClick={() => onDeleteTodo(todo.id)}
                  >
                    삭제하기
                  </StButton>
                  <StButton
                    borderColor="green"
                    onClick={() => onToggleStatusTodo(todo.id)}
                  >
                    {todo.isDone ? "취소!" : "완료!"}
                  </StButton>
                </StDialogFooter>
              </StTodoContainer>
            );
          } else {
            return null;
          }
        })}
      </StListWrapper>
```

6

취소 버튼 클릭시 기능이 작동하지 않음.
Done..! 리스트 안에 toggle에 onClick={onToggleStatusTodo}로 되어 있어 작동을 못함..

클릭시 이벤트 이뤄지게 하며 매개변수로 id받을수 있게 해줌

```
<h2 className="list-title">Done..! 🎉</h2>
      <StListWrapper>
        {todos.map((todo, index) => {
          if (todo.isDone) {
            return (
              <StTodoContainer key={todo.id}>
                <StLink to={`/${todo.id}`} key={todo.id}>
                  <div>상세보기</div>
                </StLink>
                <div>
                  <h2 className="todo-title">{todo.title}</h2>
                  <div>{todo.body}</div>
                </div>
                <StDialogFooter>
                  <StButton
                    borderColor="red"
                    onClick={() => onDeleteTodo(todo.id)}
                  >
                    삭제하기
                  </StButton>
                  <StButton
                    borderColor="green"
                    onClick={onToggleStatusTodo}
                  >
                    {todo.isDone ? "취소!" : "완료!"}
                  </StButton>
                </StDialogFooter>
              </StTodoContainer>
            );
          } else {
            return null;
          }
        })}
      </StListWrapper>
```

```
<h2 className="list-title">Done..! 🎉</h2>
      <StListWrapper>
        {todos.map((todo, index) => {
          if (todo.isDone) {
            return (
              <StTodoContainer key={todo.id}>
                <StLink to={`/${todo.id}`} key={todo.id}>
                  <div>상세보기</div>
                </StLink>
                <div>
                  <h2 className="todo-title">{todo.title}</h2>
                  <div>{todo.body}</div>
                </div>
                <StDialogFooter>
                  <StButton
                    borderColor="red"
                    onClick={() => onDeleteTodo(todo.id)}
                  >
                    삭제하기
                  </StButton>
                  <StButton
                    borderColor="green"
                    onClick={() => onToggleStatusTodo(todo.id)}
                  >
                    {todo.isDone ? "취소!" : "완료!"}
                  </StButton>
                </StDialogFooter>
              </StTodoContainer>
            );
          } else {
            return null;
          }
        })}
      </StListWrapper>
```
