# TFC CTF

## WEB

### ARE YOU THE ADMIN?

**Difficulty:** Easy

**Statement:** Do you have what it takes to be the admin?

***

The challenge provides us with the following source code : `index.tsx`

```tsx
import type { GetServerSideProps, NextPage } from "next";
import type { User } from "@prisma/client";
import { prisma } from "../globals/prisma";
import { useState } from "react";
import { useRouter } from "next/router";

type Props = {
  users: (User &
    (
      | {
          flag: string;
          isAdmin: true;
        }
      | {
          flag?: never;
          isAdmin: false;
        }
    ))[];
};

const Home: NextPage<Props> = ({ users }) => {
  const [username, setUsername] = useState("");

  const router = useRouter();

  const create = async () => {
    await fetch("/api/auth", {
      headers: {
        "Content-Type": "application/json",
      },
      method: "POST",
      body: JSON.stringify({
        username,
      }),
    });
    await router.replace(router.asPath);
  };

  return (
    <div>
      <div>Create user:</div>
      <input
        value={username}
        onChange={(event) => setUsername(event.target.value)}
      />
      <button onClick={create}>Create</button>
      <div>Users:</div>
      {users.map((user) => (
        <div key={user.id}>
          <div>Username: {user.username}</div>
          <div>Is admin? {user.isAdmin ? "yes" : "no"}</div>
          {user.isAdmin && <div>{user.flag}</div>}
        </div>
      ))}
    </div>
  );
};

export default Home;

export const getServerSideProps: GetServerSideProps<Props> = async (
  context
) => {
  const users = (await prisma.user.findMany()) as Props["users"];

  for (const user of users) {
    if (user.isAdmin) {
      user.flag = process.env.FLAG!;
    }
  }

  return {
    props: {
      users,
    },
  };
};
```

The file `auth.ts`:
```ts
import { NextApiRequest, NextApiResponse } from "next";
import { prisma } from "../../globals/prisma";

export default async function handler(
  req: NextApiRequest,
  res: NextApiResponse
) {
  const body = req.body;
  await prisma.user.create({
    data: body,
  });
  return res.status(200).end();
```

And the file 
```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"
  url      = "file:./app.db"
}

model User {
  id       String  @id @default(uuid())
  username String
  isAdmin  Boolean @default(false)
}
```

Thanks to the source code made available, we can see that it is possible to add the value `isAdmin` with the value `true` in order to authenticate with administrator rights.

When visiting the website and creating a user, the following information is returned:

![image](https://user-images.githubusercontent.com/49941629/182031138-28323457-4a7c-4c20-890c-2ad70b254c66.png)

With the Burp Suite tool, I will modify the POST request to add `isAdmin` value for get the flag!

Default POST request:

Image

Modified POST request:

Image

Flag: TFCCTF{S4n1t1z3_Y0ur_1nput5!}
