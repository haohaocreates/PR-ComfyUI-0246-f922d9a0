# ComfyUI-0246
Random nodes for ComfyUI I made to solve my struggle with ComfyUI. Have varying quality.

# Nodes list

- `Highway`: yet another implementation but overkill version of pipe and reroute.
- `Junction`: over-the-head data packing and unpacking sequentially.
- `JunctionBatch`: if `Junction` and ComfyUI batching have a kid.
- `Loop`: very hacky recursive repetiion by messing with ComfyUI internals.
- `Beautify`: the beautification of data for easy troubleshooting.

---

### Highway

<p align="center">
    <img src="https://raw.githubusercontent.com/Trung0246/ComfyUI-0246/main/assets/Screenshot%202023-11-05%20181932.png">
</p>

<details>
More complex example of what could be done (using my personal workflow as example) with extensions like `use-everywhere`:
<p align="center">
    <img src="https://raw.githubusercontent.com/Trung0246/ComfyUI-0246/main/assets/Screenshot%202023-11-06%20002520.png">
</p>

The query syntax goes as follow:

- `>name`: input variable.
- `<name`: output variable.
- `` >`n!ce n@me` ``: input variable but with special character and spaces (except `` ` ``, obviously).
- `!name`: output variable, but also delete itself, preventing from being referenced further.
  -  CURRENTLY BROKEN DUE TO HOW COMFYUI UPDATE THE NODES.
-  `<name1; >name2; !name3`: multiple input and outputs together.

For now `Highway` node is probably stable, as long as there's no cyclic connection.
  - Cyclic connection means that input and output of the same `Highway` node must not be connect, including indirect connection.
    - Else will be recursion error due to how ComfyUI execute nodes (trust me I tried).

Can probably have "nested Highway" but probably useless since the node have unlimited in-out pins.

Recommended with [chrisgoringe/cg-use-everywhere](https://github.com/chrisgoringe/cg-use-everywhere) since it allows more complex notes rerouting.

Demo workflow is in [assets/workflow_highway.json](https://github.com/Trung0246/ComfyUI-0246/blob/main/assets/workflow_highway.json).

Special thanks to [@kijai](https://github.com/kijai/ComfyUI-KJNodes) for `ConditioningMultiCombine` node as which `Highway` node is based of.
   
##### TODO (may or may not get implemented)

- Cyclic detection in JS (python probably not possible unless I figure out a way how to extract the node graph).
- Node force update (for `!name`).
</details>

---

### Junction

<p align="center">
    <img src="https://raw.githubusercontent.com/Trung0246/ComfyUI-0246/main/assets/Screenshot%202023-11-07%20040534.png">
</p>

<details>
`_offset` is used to skip data ahead for specific type (since internally it's a sequence of data).

`_offset` is persistent and will retains information across linked `Junction`.

The offset syntax goes as follow:

- `type,1`: `type` is the type (usually `LATENT`, `MODEL`, `VAE`, etc.) and `1` is the index being set.
- `type,+2`: Same as above but instead of set offset, it increase the offset instead.
- `type,-2`: Decrease offset.
- `type1, -1; type2, +2; type3, 4`: Multiple offset.

Can automatically expand pins.

Inspired by /u/GianoBifronte ideas.

Demo workflow is in [assets/workflow_junction.json](https://github.com/Trung0246/ComfyUI-0246/blob/main/assets/workflow_junction.json).
</details>

---

### Junction Batch

<p align="center">
    <img src="https://raw.githubusercontent.com/Trung0246/ComfyUI-0246/main/assets/Screenshot%202023-11-25%20034407.png">
</p>

<details>
Basically same as `Junction` but batch as list instead for further processing.

Hopefully difference between `batch` and `pluck` are self explainatory in the workflow.

Demo workflow is in [assets/workflow_junction_batch.json](https://github.com/Trung0246/ComfyUI-0246/blob/main/assets/workflow_junction_batch.json).
</details>

---

### Looping and Related

https://github.com/Trung0246/ComfyUI-0246/assets/11626920/563b9a00-412a-49b6-b1c6-24c83887a4d3

<details>
Pros:
    - Can "loop" within a single prompt queue.
Cons:
    - Will mees up the workflow very easily if not careful.

To reuse data from previous iteration, I guess there's extensions for that already.
</details>

---

### Beautify

<p align="center">
    <img src="https://github.com/Trung0246/ComfyUI-0246/assets/11626920/c95a99b2-2dee-48c4-a633-fff5da7b5069">
</p>

<details>
Recursively display structural data information, especially useful when dealing with `Highway`, `Jucntion` and `JunctionBatch`.

- `basic`: minimally shows as little as possible.
- `more`: show everything from `basic` but also shows the content. Does not expand if it meet a object.
- `full`: show everything as much as possible.
- `json`: attempt to convert the input to json and display it. Will fail if the data cannot be converted.
</details>

---

##### TODO (may or may not get implemented)

- Tutorial for `Loop`, `Count`, `Hold`, and `Random`.
- More testing.
