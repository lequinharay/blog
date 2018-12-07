+++
date = "2016-04-18T20:44:01+09:00"
draft = true
title = "CakePHPのサブコマンドに独自引数やオプションを設定する"
tags = ["PHP", "CakePHP", "CakePHP2"]

+++


// forceオプションの追加
        $parser = parent::getOptionParser();
        $parser->description('ネクストエンジンユーザーのOPL入庫実績を元に、ネクストエンジン上の在庫数を加算するコマンド');

        $subCommandOptionParser = parent::getOptionParser();
        $subCommandOptionParser->addOption('force', [
            'name' => 'force',
            'help' => "指定すると既に連携が終わった日でも入庫連携処理を再実行できる。\n※在庫加算が二重実行されるため、在庫が余分に加算される可能性があることに注意。",
            'boolean' => true,
        ]);
        $subCommandOptionParser->addArguments([
            [
                'name' => 'Company Code',
                'required' => true,
                'help' => '企業コードを指定する。'
            ],
            [
                'name' => 'target date',
                'help' => '連携対象の入庫の完了日を指定する。デフォルトは実行日の前日。'
            ]
        ]);
        $parser->addSubCommand('acc', [
            'help' => '企業コードでユーザーを指定して実行する',
            'parser' => $subCommandOptionParser,
        ]);

        return $parser;