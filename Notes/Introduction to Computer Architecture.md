SYSTEMVERILOG — UNPACKED / MULTIDIMENSIONAL / DYNAMIC ARRAYS
══════════════════════════════════════════════════════════════

CODE
────
```systemverilog
module tb;

    function void disp_msg(input string tag, input string msg);
        $display($sformatf("[%0t][%0s]: %0s", $time, tag, msg));
    endfunction

    bit [7:0] b_unpack [4];

    int arr[5];

    initial begin
        arr = '{3,3,5,2,1};
        disp_msg("Block 1", $sformatf("%0p",arr));

        arr[0:2] = '{5,3,1};
        disp_msg("Block 1", $sformatf("%0p",arr));

        arr = '{5{2}};
        disp_msg("Block 1", $sformatf("%0p",arr));

        arr = '{default:5};
        disp_msg("Block 1", $sformatf("%0p",arr));
    end

    int md_array[2][4];

    initial begin
        md_array = '{'{3,4,31,1}, '{45,1,4,1}};

        foreach(md_array[i])
            disp_msg("Block 2", $sformatf("%0p",md_array[i]));

        foreach(md_array[i,j])
            disp_msg("Block 2", $sformatf("%0d",md_array[i][j]));
    end

    bit [3:0][7:0] barray [4];

    initial begin
        barray[1] = '{8'h22, 8'h23, 8'h44, 8'h12};
        barray[2][1] = 8'h90;

        #10 barray[3][2][1:0] = 2'd2;

        foreach(barray[i]) begin
            disp_msg(
                "Block 3",
                $sformatf("BARRAY[%0d]: %0h", i, barray[i])
            );

            foreach(barray[,j]) begin
                disp_msg(
                    "Block 3",
                    $sformatf(
                        "BARRAY[%0d][%0d]: %0h",
                        i, j, barray[i][j]
                    )
                );
            end
        end
    end

    initial begin
        @(barray[3]);
        disp_msg("Block 4", "'barray' finally changed!");
    end

    int md_darray[][];

    initial begin
        md_darray = new[4];

        foreach(md_darray[i]) begin
            md_darray[i] = new[i+1];

            foreach(md_darray[,j]) begin
                md_darray[i][j] = i+j;
            end

            disp_msg(
                "BLOCK DYNAMIC ARRAY",
                $sformatf("%0p", md_darray[i])
            );
        end
    end

endmodule
```

TEXT / CONCEPTS
──────────────────────────────────────────────────────────────

Storage of unpacked arrays
───────────────────────────
Usually, SystemVerilog simulators store each element of an
unpacked array using a 32-bit word boundary.

Therefore, a byte, shortint, and int may each occupy a single
32-bit word, while a longint may occupy two words.

For example:

    bit [7:0] b_unpack [4];

This is an unpacked array of 4 elements, each 8 bits wide.

The array may therefore be stored using 4 separate 32-bit
word spaces rather than packing all four 8-bit elements into
one 32-bit word.

Simulators also usually store 4-state types such as logic
using additional storage compared with equivalent 2-state
types.

NOTE:
This is simulator implementation/storage behavior and should
not be treated as a SystemVerilog language guarantee.


Out-of-bounds access
────────────────────
Accessing an out-of-bounds index returns the default value of
the array's element type.

This applies to fixed, dynamic, queue, and associative arrays,
subject to the specific semantics of each array type.


Array literals
──────────────
An array literal can be used to initialize or assign an array.

    arr = '{3,3,5,2,1};

Assigns individual values.

    arr[0:2] = '{5,3,1};

Assigns values to an array slice.

    arr = '{5{2}};

Replicates the value 2 five times.

    arr = '{default:5};

Sets all elements to 5.


Multidimensional arrays
───────────────────────
A multidimensional array can be initialized using nested
array literals.

    int md_array[2][4];

foreach() iterates according to the range defined in each
particular dimension.

If the array is declared as:

    int md_array[1:0][3:0];

then the values of i and j are traversed in descending order
according to those declared ranges.

Observation:
Adding an empty $display produces a new line.


Packed vs unpacked dimensions
─────────────────────────────
For:

    bit [3:0][7:0] barray [4];

[4] is an unpacked dimension.

[3:0][7:0] are packed dimensions.

Therefore there are 4 unpacked elements, and each element is
32 bits wide.

Each 32-bit element consists of four 8-bit packed blocks.


Waiting for array changes
─────────────────────────
The @ operator is applicable to scalar values and packed
arrays.

Therefore, an unpacked array itself cannot simply be used as
the event expression.

However, a particular packed element can be used:

    @(barray[3]);

Here, barray[3] is a packed array.


Dynamic arrays
──────────────
Dynamic arrays can be created at runtime.

Their size cannot be changed by simply appending elements.
To change their size, a new dynamic array must be allocated.

    int arr[];

    arr = new[4];

    arr = new[8](arr);

new[8](arr) creates a new array and copies the existing
elements into it.

This has a performance cost because the existing contents
must be copied.

    arr = new[10];

This creates a new array without preserving the previous
contents.


Multidimensional dynamic arrays
───────────────────────────────
A multidimensional dynamic array can contain dynamic arrays
of different lengths.

For example:

    int md_darray[][];

can be structured such that:

    md_darray[0] → 1 element
    md_darray[1] → 2 elements
    md_darray[2] → 3 elements
    md_darray[3] → 4 elements

Each md_darray\[i\] is itself a dynamic array.