# llvmExample
### Simplest way to understand llvm module
llvmMod.cpp
``` cpp
 #include "llvm/IR/Module.h"
 #include "llvm/IR/BasicBlock.h"
 #include "llvm/IR/Function.h"
 #include "llvm/IR/LLVMContext.h"
 #include "llvm/IR/IRBuilder.h"
 #include "llvm/IR/InstrTypes.h"
 #include "llvm/IR/Type.h"
 #include "llvm/IR/CallingConv.h"
 #include <stdio.h>
  
 
 llvm::Module *createMod(llvm::LLVMContext &context);
 
 
 int main(int argc, char** argv){
   llvm::LLVMContext context;
   llvm::Module *mod = createMod(context);
 
   llvm::errs() << *mod;
 
   delete mod;
   return 0;
 }
 
 llvm::Module *createMod(llvm::LLVMContext &context){
 
   llvm::Module *mod = new llvm::Module("func", context);
  
   llvm::FunctionCallee function = mod->getOrInsertFunction("func",
       llvm::Type::getInt32Ty(context),
       llvm::Type::getInt32Ty(context),
       llvm::Type::getInt32Ty(context));
   llvm::Function *func = llvm::cast<llvm::Function>(function.getCallee());
 
   func->setCallingConv(llvm::CallingConv::C);
   llvm::Function::arg_iterator args = func->arg_begin();
   llvm::Value *x = args++;
   x->setName("x");
   llvm::Value *y = args++;
   y->setName("y");
 
   llvm::BasicBlock *bb = llvm::BasicBlock::Create(context, "entry", func);
   llvm::IRBuilder<>builder(bb);
   llvm::Value *tmp = builder.CreateBinOp(llvm::Instruction::Add, x, y, "tmp");
   builder.CreateRet(tmp);
 
   return mod;
 }

```
which compiles to:
```
; ModuleID = 'func'
source_filename = "func"

define i32 @func(i32 %x, i32 %y) {
entry:
  %tmp = add i32 %x, %y
  ret i32 %tmp
}
```
