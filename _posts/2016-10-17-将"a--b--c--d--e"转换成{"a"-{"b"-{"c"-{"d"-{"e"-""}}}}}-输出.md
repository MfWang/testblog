>`<script>
    $(function(){
        var str="a->b->c->d->e"; 
        /*法一*/ 
        var arr = str.split('->'); 
        var src= ""; 
        for(var i=arr.length-1;i>=0;i--){
            var obj = {}; 
            obj[arr[i]]=src; 
            src=obj; 
        }
        console.log(JSON.stringify(src)); 
        /*法二*/ 
        var arr = str.split('->'); 
        var src='{'; 
        var num=0; 
        var i = 0;
        var len = arr.length; 
        for(i=0;i<arr.length;i++){
            src += '"'+arr[i]+'":'; 
            if(++num <= len-1){
                src += '{'; 
            }
        }
        src += '""'; 
        for(i=0;i<=len-1;i++)
            src += '}'; 
        console.log(src); 
        console.log(eval('('+src+')')); 
        /*法三*/ 
        var str="a->b->c->d->e"; 
        var reg=/(\w+)(->)?/gi; 
        var arr; 
        var src={},tmp=src; 
        while(arr=reg.exec(str)){
            tmp[arr[1]]={}; 
            tmp=tmp[arr[1]]; 
        }
        console.log(JSON.stringify(src)); 
        /*法四*/ 
        var arr = str.split('->'); 
        var src= ""; 
        arr.reverse().map(function(i) {
            var obj = {}; 
            obj[i]=src; 
            src=obj; 
        }); 
        console.log(JSON.stringify(src)); 
        /*法五*/ 
        var list = ['a','b','c','d','e']; 
        var i= 0,code = 'var _c = {};'; 
        var init = '_c[]={};'; 
        for (;i<list.length;i++){
            init = init.replace('[]','["'+list[i]+'"][]'); 
            code += init.replace('[]',''); 
        }
        console.log(eval('(function(){'+code+'return _c;})()'));
});
</script>`

