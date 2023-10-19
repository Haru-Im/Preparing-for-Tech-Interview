# Utility Types

Utility Types은 TypeScript에서 제공하는 제네릭 타입이다. 이를 사용하면 기존의 타입을 변환하여 복잡한 타입을 쉽게 만들 수 있다. 여기서는 Partial, Pick, Omit 등의 Utility Types을 소개하고, 각각 어떻게 사용되는지 예시를 통해 설명한다.

1. **Partial**

   - `Partial<T>`은 `T` 타입의 모든 속성을 선택적(Optional) 속성으로 변환한다. 이 유틸리티는 기존 타입의 모든 프로퍼티를 optional로 만들어 새로운 타입을 생성한다. 주로 전체 속성을 갖추지 않은 객체를 다룰 때 유용하다.

   ```typescript
   interface Todo {
     title: string;
     description: string;
   }

   function updateTodo(todo: Todo, fieldsToUpdate: Partial<Todo>) {
     return { ...todo, ...fieldsToUpdate };
   }

   const todo1: Todo = {
     title: "organize desk",
     description: "clear clutter",
   };

   // 모든 속성을 제공할 필요가 없다.
   const todo2 = updateTodo(todo1, {
     description: "new description",
   });
   ```

2. **Pick**

   - `Pick<T, K>`은 `T` 타입에서 특정 속성 `K`만 추출하여 타입을 구성한다. 이 유틸리티는 기존 타입의 일부만 가져와 새로운 타입을 만들 때 유용하다.

   ```typescript
   interface Task {
     title: string;
     description: string;
     completed: boolean;
   }

   // 'Task'에서 'title'과 'completed' 속성만 가져온다.
   type TaskPreview = Pick<Task, "title" | "completed">;

   const task1: TaskPreview = {
     title: "Clean room",
     completed: false,
   };
   ```

3. **Omit**

   - `Omit<T, K>`은 `T` 타입에서 특정 속성 `K`를 제외한 나머지로 타입을 구성한다. 이 유틸리티는 특정 필드를 제외한 새로운 타입을 만들 때 유용하다.

   ```typescript
   interface Schedule {
     title: string;
     description: string;
     date: Date;
   }

   // 'Schedule'에서 'date' 필드를 제외한다.
   type SchedulePreview = Omit<Schedule, "date">;

   const meeting: SchedulePreview = {
     title: "Board meeting",
     description: "Discuss business",
   };
   ```

이러한 Utility Types은 코드의 재사용성을 높여주며, 타입 관리를 효과적으로 돕는다. 타입스크립트를 사용하는 개발자가 타입을 좀 더 유연하게, 그리고 안정적으로 활용할 수 있도록 도와주는 강력한 도구이다.
