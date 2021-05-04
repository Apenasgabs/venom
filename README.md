# 🕷Venom Bot🕸

![enter image description here](https://s2.click/venom.jpg)

> Venom é um sistema de alto desempenho desenvolvido em JavaScript para criar um bot para WhatsApp, suporte para criação de qualquer interação, como atendimento ao cliente, envio de mídia, reconhecimento de frases baseado em inteligência artificial e todos os tipos de arquitetura de design para WhatsApp.

## 🕷🕷 Funções do  Venom🕷🕷

|                                                            |     |
| ---------------------------------------------------------- | --- |
| Atualização automatica do QR Code                          | ✔   |
| Envia **text, image, video, audio and docs**               | ✔   |
| Obtenha **contatos, bate-papos, grupos, membros do grupo, lista de bloqueio**	| ✔   |
| Envia contatos                                              | ✔   |
| Envia adesivos                                              | ✔   |
| Envia adesivos GIF                                          | ✔   |
| Sessões Múltiplas	                                          | ✔   |
| Encaminhar mensagens                                        | ✔   |
| Receber mensagem                                            | ✔ |
| 📍 Enviar localização !!                                    | ✔ |
| 🕸🕸 ** e muito mais **                                      | ✔ |

## Instalação

```bash
> npm i --save venom-bot
```

## Começando

```javascript
// Supports ES6
// import { create, Whatsapp } from 'venom-bot';
const venom = require('venom-bot');

venom
  .create()
  .then((client) => start(client))
  .catch((erro) => {
    console.log(erro);
  });

function start(client) {
  client.onMessage((message) => {
    if (message.body === 'Hi' && message.isGroupMsg === false) {
      client
        .sendText(message.from, 'Welcome Venom 🕷')
        .then((result) => {
          console.log('Result: ', result); //retorna o object com successo
        })
        .catch((erro) => {
          console.error('Error when sending: ', erro); //retorna o object error
        });
    }
  });
}
```

##### Depois de executar a função `create ()`, ** venom ** irá criar uma instância do Whatsapp web. Se você não estiver logado, ele imprimirá um código QR no terminal. Digitalize com o seu telefone e você está pronto para começar!

##### Venom se lembrará da sessão, então não há necessidade de autenticar todas as vezes.

##### Sessões múltiplas podem ser criadas ao mesmo tempo, passando um nome de sessão para a função `create ()`:

```javascript
// Inicia bot whatsapp de vendas
venom.create('sales').then((salesClient) => {...});

// Inicia bot whatsapp de suporte
venom.create('support').then((supportClient) => {...});
```

<br>

## Criar parâmetros opcionais

Venom `create()` method third parameter can have the following optional parameters:

```javascript
const venom = require('venom-bot');

venom
  .create(
    'sessionName',
    (base64Qrimg, asciiQR) => {
      console.log('Terminal qrcode: ', asciiQR);
      console.log('base64 image string qrcode: ', base64Qrimg);
    },
    (statusSession) => {
      console.log('Status Session: ', statusSession); //retorna isLogged || notLogged || browserClose || qrReadSuccess || qrReadFail || autocloseCalled
    },
    {
      folderNameToken: 'tokens', //Nome da pasta quando salvo o token
      mkdirFolderToken: '', //diretorio da pasta de tokens, dentro da pasta, exemplo:  { mkdirFolderToken: '/node_modules', } //irá salvar a pasta de tokens no diretório node_modules
      headless: true, // Headless chrome
      devtools: false, // Abre devtools por padrao
      useChrome: true, // Se for falso, usará a instância do Chromium
      debug: false, // Abre uma sessão de depuração
      logQR: true, // Registra QR automaticamente no terminal
      browserArgs: [''], // Parameters to be added into the chrome browser instance
      disableSpins: true, // Desativará a animação do Spinnies, útil para contêineres (docker) para um melhor registro
      disableWelcome: true, // Irá desativar a mensagem de boas-vindas que aparece no início
      updatesLog: true, // Registra atualizações de informações automaticamente no terminal
      autoClose: 60000, // Fecha automaticamente o venom-bot apenas ao escanear o código QR (padrão 60 segundos, se você quiser desligá-lo, atribua 0 ou falso)
    }
  )
  .then((client) => {
    start(client);
  })
  .catch((erro) => {
    console.log(erro);
  });
```

## Sessão de status de retorno de chamada

Obtém o retorno se a sessão for `isLogged` ou` notLogged` ou `browserClose` ou` qrReadSuccess` ou `qrReadFail` ou` autocloseCalled`

##### `isLogged: Quando o usuário já está logado no navegador`.
##### `notLogged: Quando o usuário não está conectado ao navegador, é necessário fazer a varredura do código QR pelo celular na opção WharsApp Web`.

##### `browserClose: Se o navegador for fechado este parâmetro é retornado`.

##### `qrReadSuccess: se o usuário não estiver logado, o código QR é passado no terminal e um retorno de chamada é retornado. Após a leitura correta pelo celular este parâmetro é retornado.

##### `qrReadFail: Se o navegador parar quando a varredura do código QR estiver em andamento, este parâmetro é retornado`.

##### `autocloseCalled: O navegador foi fechado usando o comando autoClose`.

```javascript
const venom = require('venom-bot');
venom
  .create(
    'sessionName',
    undefined,
    (statusSession) => {
      console.log('Status Session: ', statusSession);
      //return isLogged || notLogged || browserClose || qrReadSuccess || qrReadFail || autocloseCalled
    },
    undefined
  )
  .then((client) => {
    start(client);
  })
  .catch((erro) => {
    console.log(erro);
  });
```

## Exportando código QR

Por padrão, o código QR aparecerá no terminal. Se você precisa passar o QR
em outro lugar aqui está como:

```javascript
const fs = require('fs');
const venom = require('venom-bot');

venom
  .create(
    'sessionName',
    (base64Qr, asciiQR) => {
      console.log(asciiQR); // Optional to log the QR in the terminal
      var matches = base64Qr.match(/^data:([A-Za-z-+\/]+);base64,(.+)$/),
        response = {};

      if (matches.length !== 3) {
        return new Error('Invalid input string');
      }
      response.type = matches[1];
      response.data = new Buffer.from(matches[2], 'base64');

      var imageBuffer = response;
      require('fs').writeFile(
        'out.png',
        imageBuffer['data'],
        'binary',
        function (err) {
          if (err != null) {
            console.log(err);
          }
        }
      );
    },
    undefined,
    { logQR: false }
  )
  .then((client) => {
    start(client);
  })
  .catch((erro) => {
    console.log(erro);
  });
```

## Baixando arquivos

O Puppeteer cuida do download do arquivo. A descriptografia está sendo feita como
o mais rápido possível (ultrapassa os métodos nativos). Suporta arquivos grandes!

```javascript
import fs = require('fs');
import mime = require('mime-types');

client.onMessage( async (message) => {
  if (message.isMedia === true || message.isMMS === true) {
    const buffer = await client.decryptFile(message);
    // Neste ponto, você pode fazer o que quiser com o buffer
    // Provavelmente você deseja gravá-lo em um arquivo
    const fileName = `some-file-name.${mime.extension(message.mimetype)}`;
    await fs.writeFile(fileName, buffer, (err) => {
      ...
    });
  }
});
```

## Funções básicas (uso)

Nem todas as funções disponíveis são listadas, para uma análise mais detalhada, todas as funções
disponíveis podem ser encontrados em [aqui](/src/api/layers) and
[aqui](/src/lib/wapi/functions)

### Conversando

##### Aqui, `chatId` podera ser` <phoneNumber> @ c.us` ou `<phoneNumber> - <groupId> @ g.us`

```javascript
// Send contact
await client
  .sendContactVcard('000000000000@c.us', '111111111111@c.us', 'Name of contact')
  .then((result) => {
    console.log('Result: ', result); //return object success
  })
  .catch((erro) => {
    console.error('Error when sending: ', erro); //return object error
  });

// Send a list of contact cards
await client
  .sendContactVcardList('000000000000@c.us', [
    '111111111111@c.us',
    '222222222222@c.us',
  ])
  .then((result) => {
    console.log('Result: ', result); //return object success
  })
  .catch((erro) => {
    console.error('Error when sending: ', erro); //return object error
  });

// Send basic text
await client
  .sendText('000000000000@c.us', '👋 Hello from venom!')
  .then((result) => {
    console.log('Result: ', result); //return object success
  })
  .catch((erro) => {
    console.error('Error when sending: ', erro); //return object error
  });

// Send location
await client
  .sendLocation('000000000000@c.us', '-13.6561589', '-69.7309264', 'Brasil')
  .then((result) => {
    console.log('Result: ', result); //return object success
  })
  .catch((erro) => {
    console.error('Error when sending: ', erro); //return object error
  });

// Automatically sends a link with the auto generated link preview. You can also add a custom message to be added.
await client
  .sendLinkPreview(
    '000000000000@c.us',
    'https://www.youtube.com/watch?v=V1bFr2SWP1I',
    'Kamakawiwo ole'
  )
  .then((result) => {
    console.log('Result: ', result); //return object success
  })
  .catch((erro) => {
    console.error('Error when sending: ', erro); //return object error
  });

// Send image (you can also upload an image using a valid HTTP protocol)
await client
  .sendImage(
    '000000000000@c.us',
    'path/to/img.jpg',
    'image-name',
    'Caption text'
  )
  .then((result) => {
    console.log('Result: ', result); //return object success
  })
  .catch((erro) => {
    console.error('Error when sending: ', erro); //return object error
  });

// Send file (venom will take care of mime types, just need the path)
// you can also upload an image using a valid HTTP protocol
await client
  .sendFile(
    '000000000000@c.us',
    'path/to/file.pdf',
    'file_name',
    'See my file in pdf'
  )
  .then((result) => {
    console.log('Result: ', result); //return object success
  })
  .catch((erro) => {
    console.error('Error when sending: ', erro); //return object error
  });

// Sends file
// base64 parameter should have mime type already defined
await client
  .sendFileFromBase64(
    '000000000000@c.us',
    base64PDF,
    'file_name.pdf',
    'See my file in pdf'
  )
  .then((result) => {
    console.log('Result: ', result); //return object success
  })
  .catch((erro) => {
    console.error('Error when sending: ', erro); //return object error
  });

// Generates sticker from the provided animated gif image and sends it (Send image as animated sticker)
// image path imageBase64 A valid gif image is required. You can also send via http/https (http://www.website.com/img.gif)
await client
  .sendImageAsStickerGif('000000000000@c.us', './image.gif')
  .then((result) => {
    console.log('Result: ', result); //return object success
  })
  .catch((erro) => {
    console.error('Error when sending: ', erro); //return object error
  });
// gera uma figurinha a partir de uma imagem e envia ela como figurinha
// Generates sticker from given image and sends it (Send Image As Sticker)
// image path imageBase64 A valid png, jpg and webp image is required. You can also send via http/https (http://www.website.com/img.jpg)
await client
  .sendImageAsSticker('000000000000@c.us', './image.jpg')
  .then((result) => {
    console.log('Result: ', result); //return object success
  })
  .catch((erro) => {
    console.error('Error when sending: ', erro); //return object error
  });

// Send @tagged message
await client.sendMentioned(
  '000000000000@c.us',
  'Hello @5218113130740 and @5218243160777!',
  ['5218113130740', '5218243160777']
);

// Reply to a message
await client.reply(
  '000000000000@c.us',
  'This is a reply!',
  message.id.toString()
);

// Reply to a message with mention
await client.reply(
  '000000000000@c.us',
  'Hello @5218113130740 and @5218243160777! This is a reply with mention!',
  message.id.toString(),
  ['5218113130740', '5218243160777']
);

// Send gif
await client.sendVideoAsGif(
  '000000000000@c.us',
  'path/to/video.mp4',
  'video.gif',
  'Gif image file'
);

// Forwards messages
await client.forwardMessages(
  '000000000000@c.us',
  [message.id.toString()],
  true
);

// Send seen ✔️✔️
await client.sendSeen('000000000000@c.us');

// Start typing...
await client.startTyping('000000000000@c.us');

// Stop typing
await client.stopTyping('000000000000@c.us');

// Set chat state (0: Typing, 1: Recording, 2: Paused)
await client.setChatState('000000000000@c.us', 0 | 1 | 2);
```

## Retrieving Data

```javascript
// Calls your list of blocked contacts (returns an array)
const getBlockList = await client.getBlockList();

// Retrieve contacts
const contacts = await client.getAllContacts();

// Retrieve messages in chat
const Messages = await client.getAllMessagesInChat('000000000000@c.us');

// Retrieve more chat message
const moreMessages = await client.loadEarlierMessages('000000000000@c.us');

// Retrieve all messages in chat
const allMessages = await client.loadAndGetAllMessagesInChat(
  '000000000000@c.us'
);

// Retrieve contact status
const status = await client.getStatus('000000000000@c.us');

// Retrieve user profile
const user = await client.getNumberProfile('000000000000@c.us');

// Retrieve all unread message
const messages = await client.getAllUnreadMessages();

// Retrieve all chats
const chats = await client.getAllChats();

// Retrieve all groups
const chats = await client.getAllGroups();

// Retrieve profile fic (as url)
const url = await client.getProfilePicFromServer('000000000000@c.us');

// Retrieve chat/conversation
const chat = await client.getChat('000000000000@c.us');

// Check if the number exists
const chat = await client.checkNumberStatus('000000000000@c.us');
```

## Funções de Grupo

```javascript
// groupId or chatId: leaveGroup 52123123-323235@g.us

// Leave group
await client.leaveGroup('00000000-000000@g.us');

// Get group members
await client.getGroupMembers('00000000-000000@g.us');

// Get group members ids
await client.getGroupMembersIds('00000000-000000@g.us');

// Generate group invite url link
await client.getGroupInviteLink('00000000-000000@g.us');

// Create group (title, participants to add)
await client.createGroup('Group name', [
  '111111111111@c.us',
  '222222222222@c.us',
]);

// Remove participant
await client.removeParticipant('00000000-000000@g.us', '111111111111@c.us');

// Add participant
await client.addParticipant('00000000-000000@g.us', '111111111111@c.us');

// Promote participant (Give admin privileges)
await client.promoteParticipant('00000000-000000@g.us', '111111111111@c.us');

// Demote particiapnt (Revoke admin privileges)
await client.demoteParticipant('00000000-000000@g.us', '111111111111@c.us');

// Get group admins
await client.getGroupAdmins('00000000-000000@g.us');

// Return the group status, jid, description from it's invite link
await client.getGroupInfoFromInviteLink(InviteCode);

// Join a group using the group invite code
await client.joinGroup(InviteCode);
```

## Funções de Perfil

```javascript
// Set client status
await client.setProfileStatus('On vacations! ✈️');

// Set client profile name
await client.setProfileName('Venom bot');

// Set client profile photo
await client.setProfilePic('path/to/image.jpg');
```

## Funções do dispositivo

```javascript
// Delete the Service Worker
await client.killServiceWorker();

// Load the service again
await client.restartService();

// Get device info
await client.getHostDevice();

// Get connection state
await client.getConnectionState();

// Get battery level
await client.getBatteryLevel();

// Is connected
await client.isConnected();

// Get whatsapp web version
await client.getWAVersion();
```

## Eventos

```javascript
// Listen to messages
client.onMessage(message => {
  ...
})

// Listen to state changes
client.onStateChange(state => {
  ...
});

// Listen to ack's
client.onAck(ack => {
  ...
});

// Listen to live location
// chatId: 'phone@c.us'
client.onLiveLocation("000000000000@c.us", (liveLocation) => {
  ...
});

// chatId looks like this: '5518156745634-1516512045@g.us'
// Event interface is in here: https://github.com/s2click/venom/blob/master/src/api/model/participant-event.ts
client.onParticipantsChanged("000000000000@c.us", (event) => {
  ...
});

// Listen when client has been added to a group
client.onAddedToGroup(chatEvent => {
  ...
});

```

## Outros

```javascript
// Pin chat and Unpin chat messages with true or false
// Pin chat, non-existent (optional)
await client
  .pinChat(chatId, true, false)
  .then((result) => {
    console.log('Result: ', result); //return object success
  })
  .catch((erro) => {
    console.error('Error when sending: ', erro); //return object error
  });

// Change the theme
// string types "dark" or "light"
await client.setTheme('dark');

// Receive the current theme
// returns string light or dark
await client.getTheme();

// Delete chat
await client.deleteChat('000000000000@c.us');

// Clear chat messages
await client.clearChat('000000000000@c.us');

// Archive and unarchive chat messages with true or false
await client.archiveChat(chatId, true);

// Delete message (last parameter: delete only locally)
await client.deleteMessage('000000000000@c.us', message.id.toString(), false);

// Mark chat as not seen (returns true if it works)
await client.markUnseenMessage('000000000000@c.us');

// Blocks a user (returns true if it works)
await client.blockContact('000000000000@c.us');

// Unlocks contacts (returns true if it works)
await client.unblockContact('000000000000@c.us');

// Retrieve a number profile / check if contact is a valid whatsapp number
const profile = await client.getNumberProfile('000000000000@c.us');
```

## Diversos

Existem alguns truques para um melhor aproveitamento do venom.

#### Mantenha a sessão ativa:

```javascript
// In case of being logged out of whatsapp web
// Force it to keep the current session
// State change
// Detect a logout
// Possible state values:
// CONFLICT
// CONNECTED
// DEPRECATED_VERSION
// OPENING
// PAIRING
// PROXYBLOCK
// SMB_TOS_BLOCK
// TIMEOUT
// TOS_BLOCK
// UNLAUNCHED
// UNPAIRED
// UNPAIRED_IDLE

client.onStateChange((state) => {
  console.log('State changed: ', state);
  const conflits = [
    venom.SocketState.CONFLICT,
    venom.SocketState.UNPAIRED,
    venom.SocketState.UNLAUNCHED,
  ];
  if (conflits.includes(state)) {
    client.useHere();
    // Detect a logout
    if (state === 'UNPAIRED') console.log('Client logout!');
  }
});
```

#### Sessões múltiplas

Se você precisar executar várias sessões de uma vez, basta passar um nome de sessão para
método `create ()`, não use hífen para o nome das sessões.

```javascript
async () => {
  const marketingClient = await venom.create('marketing');
  const salesClient = await venom.create('sales');
  const supportClient = await venom.create('support');
};
```

#### Fechando (salvando) sessões

Feche a sessão adequadamente para garantir que ela seja salva para a próxima vez que você
faça login (para que não seja solicitada a varredura QR novamente). Então, em vez de CTRL + C,

```javascript
// Catch ctrl+C
process.on('SIGINT', function() {
  client.close();
});

// Try-catch close
try {
   ...
} catch (error) {
   client.close();
}
```

### Fechamento automático de sessões não sincronizadas

O fechamento automático é habilitado por padrão e o tempo limite é definido como 60 seg.
Recebe o tempo em milissegundos para a contagem regressiva até o emparelhamento.

Use "autoClose: 0 | false" para desativar o fechamento automático.
### Depuração

## Desenvolvimento
Construir o veneno é muito simples, embora contenha 3 projetos principais dentro

1. Projeto Wapi

```bash
> npm run build:wapi
```

2. Middleware

```bash
> npm run build:middleware
> npm run build:jsQR
```

3. Venom

```bash
> npm run build:venom
```

Para construir o projeto inteiro basta executar

```bash
> npm run build
```

## Mantenedores

Os mantenedores são necessários, não posso manter todas as atualizações sozinho. Se você é
interessado, abra um Pull Request.

## Contribuindo

Solicitações pull são bem-vindas. Para mudanças importantes, abra um problema primeiro para
discuta o que você gostaria de mudar.
