# Delta Modeling Section - Mermaid Charts

To generate sleek mermaid charts that are clickable and well-formatted, use this repo.

1. Create a conda environment using the environment.yml in this repo
    > conda env create -f environment.yml

2. Download the mermaid cli SVG generator

    > npm config set registry http://registry.npmjs.org/
    
    > npm config set strict-ssl false
    
    > npm install -g @mermaid-js/mermaid-cli

3. Create your mermaid diagrams and save as .mmd

4. Export as SVG

    > mmdc -i input.mmd -o output.svg

There is an [example.md](diagrams/example.md) from [PetterTech](https://github.com/PetterTech/DemoStuff/blob/main/Mermaid/mermaid.md)
There are also examples of how to just include a mermaid diagram in your readme.md [here](https://gist.github.com/ChristopherA/bffddfdf7b1502215e44cec9fb766dfd)

You can also use a VS Code extension but it only works if your file is *.mmd and has the code block:

> \`\`\`mermaid
> 
> diagram code
> 
> \`\`\`

Around the diagram code.
The extension we're using is Markdown Preview Mermaid Support by Matt Bierner
