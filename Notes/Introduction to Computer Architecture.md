
Arrays can be stored in 32- bit unpacked words or 32-bit packed words which goes for an optimization route not necessarily going for the coding aspect

```systemverilog
int arr[5];
bit [31:0] parr; // here is a comment that will change the way this looks

initial begin
	
end
```



```
module tb;

    function void disp_msg(input string tag, input string msg);

        $display($sformatf("[%0t][%0s]: %0s", $time, tag, msg));

    endfunction
```

    // Usually system-verilog simulators store each element of an unpacked array in a 32-bit word boundary.

    // So a byte, shortint, and int are all stored in a single word whereas longint takes 2 word.

    bit [7:0] b_unpack [4]; // Unpacked array of 4 elements each is 8 bits wide

    // The above variable will be stored in 4 32-bit word spaces instead of a single 32-bit word divided into 4 parts.

    // On the same note, simulators, usually store 4-state types like 'logic' in two or more consecutive words (twice of 2-state variables)

    //=======================

    // Accessing an out of bound index returns the default value of the array type.

    // Applies for all array types - fixed, dynamic, queue or associative

    //=======================

    //=======================

    // How to declare an array literal?

    int arr[5];

    initial begin

        arr = '{3,3,5,2,1};

        disp_msg("Block 1", $sformatf("%0p",arr));       

        arr[0:2] = '{5,3,1};

        disp_msg("Block 1", $sformatf("%0p",arr));

        arr = '{5{2}};

        disp_msg("Block 1", $sformatf("%0p",arr));

        arr = '{default:5}; // All elements are set to 5

        disp_msg("Block 1", $sformatf("%0p",arr));

    end

    //=======================

    //=======================

    // How to initialize and walk through a muti dimensional array

    int md_array[2][4];

    initial begin

        md_array = '{'{3,4,31,1}, '{45,1,4,1}};

        foreach(md_array[i]) disp_msg("Block 2", $sformatf("%0p",md_array[i]));

        foreach(md_array[i,j]) disp_msg("Block 2", $sformatf("%0d",md_array[i][j]));

    end

    // foreach() interates as per the range defined in that particular dimension.

    // If md_array was defined as md_array[1:0][3:0] then values of i and j will be in descending order

    // Interesting fact: Adding an empty $display returns a new line

    //=======================

    //=======================

    // Visualization of unpacked arrays:

    bit [3:0][7:0] barray [4];

    // 4 elements each containing 4 blocks and each block has 8 sub-blocks of 1 bit width

    // While the elements are "unpacked", the blocks and sub-blocks here are packed

    // So total space acquired might be 4 32-words in total

    initial begin

        barray[1] = '{8'h22, 8'h23, 8'h44, 8'h12};

        barray[2][1] = 8'h90;

        #10 barray[3][2][1:0] = 2'd2;

        foreach(barray[i]) begin

            disp_msg("Block 3",$sformatf("BARRAY[%0d]: %0h", i, barray[i]));

            foreach(barray[,j]) begin

                disp_msg("Block 3",$sformatf("BARRAY[%0d][%0d]: %0h", i, j, barray[i][j]));

            end

        end

    end

    // If we want to wait for a change in an array, we we have to use packed array.

    // The operator "@" is applicable only for scalar values and packed arrays.

    // But we can block on a particular element of the array like barray[3]

    initial begin

        @(barray[3]);

        disp_msg("Block 4", "'barray' finally changed!");

    end   

    //=======================

    //=======================

    // Dynamic arrays

    // They can be created during run time but if we want to change their sizes, we need to re-create them. We can't ust append.

    // int arr[];

    // int darr[] = '{2,3,2,5,72,4,1,4}; //  Dynamic array

    // int sarr[4];

    // initial begin

            // sarr = '{3,5,3,1};

            // arr = new[4](saar);

            // disp_msg("MISC BLOCK", $sformatf("%0p", arr));

            // arr = new[8](arr); // copies previous elements but this has a performance issue has it copies the entire contents of an array

            // arr = new[10]; // Deletes previous elements

    // end

    // Muti-dimensional arrays can be arrays of varrying length arrays.

    // If we have md_darray[][], then we can have md_array[0].size = 1, md_array[1].size = 5,....

    int md_darray[][];

    initial begin

        md_darray = new[4];

        foreach (md_darray[i]) begin

            md_darray[i] = new[i+1];

            foreach(md_darray[,j]) begin

                md_darray[i][j] = i+j;

            end

            disp_msg("BLOCK DYNAMIC ARRAY", $sformatf("%0p", md_darray[i]));

        end

    end

Endmodule

int arr[5];

bit [31:0] parr;

initial begin

end