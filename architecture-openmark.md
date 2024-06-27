[<div>

  <div align="center">
    <a href="openmark.io" target="_blank" rel="noreferrer">
    <img src="https://res.cloudinary.com/synasapmob/image/upload/v1719391982/b3cfb2dc142230a65a44bc8e53894231.png" height="250" />
    </a>
  </div>

  <h3 align="center">OpenMark - FrontEnd<h3/>

  <div align="center">
    <img src="https://assets.vercel.com/image/upload/v1662130559/nextjs/Icon_light_background.png" width="32"height="32" />
    <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/typescript/typescript-original.svg" width="32"height="32" />
    <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSiHG7TDP4O6sIQ3-4H-71P4Ciy-sk3npDRsA&s" width="32"height="32" />
  </div>
  
</div>

## Architecture

Scalability is crucial for projects. We implemented a **system container** to enhance scalability. The **system container** mission is to pull folders and render them, 
effectively managing multiple projects within a single repository.

1. **Initialization:**
   You can easily clone other chains based on the initial framework. This allows you to quickly set up new projects by following the established structure.
   
3. **Maintainability:**
   The **system container** separates projects in a way that prevents overlap, such as themes, layouts, etc.
   This separation ensures that changes in one project do not impact others.
   
5. **Clean code:**
   We adhere to the **SOLID principles**, ensuring that our code is readable, maintainable, and easy to modify. This fosters better collaboration and makes it easier for team members to work together.

<div>
    <img src="https://res.cloudinary.com/synasapmob/image/upload/v1719400238/abeeae836b3f42d25fa28f2c6802a321.png" />
</div>

## How to installation?

To run the application without requiring a build setup, follow these installation steps.<br />
Ensure that you have all the necessary **packages** installed.

```bash
# Clone the repository
git clone https://github.com/grindytech/openmark-fe.git

# Change to project directory
cd openmark-fe

# Download project dependencies
yarn install

# Run the server
yarn  dev
```
](https://github.com/synasapmob/documentation/blob/main/architecture-openmark.md)
