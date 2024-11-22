# LibXModel-js

This library allows you to load Call of Duty XModel files in Javascript.

The code was translated to JS from:
https://github.com/mauserzjeh/cod-asset-importer

I've only tested this with xmodel files from Call of Duty 1 and Call of Duty: United Offensive.

## Example Usage

```ts
async function getFileBytes(path: string): Promise<Uint8Array> {
    const response = await fetch(path)
    const buffer = await response.arrayBuffer()
    return new Uint8Array(buffer);
}

const xmodelBytes = await getFileBytes('xmodel/my-model-file')
const xmodelLoader = new XModelLoader(xmodelBytes)
const model = await xmodelLoader.load('my-model-file', GameVersion.CoD)

const firstLod = model.lods[0]
const xmodelPartBytes = await getFileBytes(`xmodelparts/${firstLod.name}`)
const xmodelPartLoader = new XModelPartLoader(xmodelPartBytes)
const modelPart = xmodelPartLoader.load(firstLod.name)

for (let lod of model.lods) {
    console.log(lod.name, 'LOD textures:')
    for (let texturePath of lod.materials) {
        console.log(`- skins/${texturePath}`)
    }
    
    const surfBytes = await getFileBytes(`xmodelsurfs/${lod.name}`)
    const surfLoader = new XModelSurfLoader(surfBytes)
    const surf = await surfLoader.load(lod.name, modelPart)

    for (let surface of surf.surfaces) {
        console.log(surface)

        /*
         * mesh.positions = surface.vertices.flatMap(x => x.position)
         * mesh.normals = surface.vertices.flatMap(x => x.normal)
         * mesh.uvs = surface.vertices.flatMap(x => x.uv)
         * mesh.colors = surface.vertices.flatMap(x => x.color)
         * mesh.indices = surface.triangles
         */
    }
}

console.log('Collision LOD index: ', model.collisionLodIndex)
```