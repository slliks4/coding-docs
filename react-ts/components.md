# FC (Funtion Component)
💡 Why use FC (FunctionComponent)?

    It tells TypeScript this is a React functional component

    It infers that children, props, etc. are valid if needed

    It's optional — but helpful for consistency and IDE support

# PropsWithChildren
import { PropsWithChildren } from 'react';

type LayoutProps = {
  title: string;
};

const Layout = ({ title, children }: PropsWithChildren<LayoutProps>) => {
  return (
    <div>
      <h1>{title}</h1>
      <main>{children}</main>
    </div>
  );
};
