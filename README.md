# Atividade9

trigger AccountT3 on Account (before insert, before update, after insert) {
    
    //Trigger vai agir antes da inserção de dados.
    if(trigger.isBefore){
        //Trigger vai ser acionado no momento da inserção.
        if(trigger.isInsert){
            //Usando o loop for para acessar cada linha dos meus registros.
            for(Account acc : Trigger.new){
                //Condição para verificar se a Receita anual é igual a nulo ou menor que 0.
                if(acc.AnnualRevenue == null || acc.AnnualRevenue < 0){
                    //Se condição for verdadeira. Mensagem de erro para impedir o usuário de criar um registro com Receita anual nula e menor que 0.
                    acc.AnnualRevenue.addError('Campo inválido');
                }//Condição para verificar se a Receita anual é menor que 100000.
                else if(acc.AnnualRevenue < 100000){
                    //Se não se colocar Classificação como Cold
                    acc.Rating = 'Cold';
                }//Condição para verificar se a Receita anual é maior ou igual a 100000 e menor que 500000.
                else if(acc.AnnualRevenue >= 100000 && acc.AnnualRevenue < 500000){
                    //Se não se colocar Classificação como Warm
                    acc.Rating = 'Warm';
                }//Condição para verificar se a Receita anual é maior ou igual a 500000.
                else if(acc.AnnualRevenue >=500000){
                    //Se não se colocar Classificação como Hot
                    acc.Rating = 'Hot';
                }
            }
        }//Trigger vai ser acionado no momento do update.
        if(trigger.isUpdate){
            //Usando o loop for para acessar cada linha dos meus registros.
            for(Account acc : Trigger.new){
                //Condição para verificar se a Receita anual é maior ou igual a 100000 e menor que 500000 e classificação diferente de Warm.
                if((acc.AnnualRevenue >= 100000 && acc.AnnualRevenue < 500000) && acc.Rating != 'Warm'){
                    //Se condição for verdadeira. Mensagem de erro para impedir o usuário de criar um registro com a Classificação diferente de Warm.
                    acc.Rating.addError('Não é possivel fazer alteração');
                }//Condição para verificar se a Receita anual é maior ou igual a 500000 e classificação diferente de Hot.
                if(acc.AnnualRevenue >=500000 && acc.Rating != 'Hot'){
                    //Se condição for verdadeira. Mensagem de erro para impedir o usuário de criar um registro com a Classificação diferente de Hot.
                    acc.Rating.addError('Não é possivel fazer alteração');
                }
            }
        }
    }
    //Trigger vai agir depois da inserção de dados.
    if(trigger.isAfter){
        //Trigger vai ser acionado no momento da inserção.
        if(trigger.isInsert){
            //Usando o loop for para acessar cada linha dos meus registros.
            for(Account acc : Trigger.new){
                //Criando um novo Objeto oportunidade.
                Opportunity opportunity = new Opportunity(
                Name = acc.Name,
                Amount = 0,
                CloseDate = Date.today().addDays(15),
                StageName = 'Prospecting'
                );
                //Inserção do Objeto oportunidade.
                Insert opportunity;
                //Condição para verificar se a Receita anual é maior ou igual a 500000.
                if(acc.AnnualRevenue >=500000){
                    //Se for verdadeira, criar uma nova tarefa
                    //Criando um novo Objeto tarefa.
                    Task task = new Task(
                    WhatId = acc.Id,
                    Description = 'Agendar Reunião',
                    ActivityDate = date.today().addDays(1)    
                    );
                    //Inserção do Objeto tarefa.
                    Insert task;
                }
            }
        }
    }
}
