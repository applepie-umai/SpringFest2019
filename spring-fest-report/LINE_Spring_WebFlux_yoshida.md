# LINE�����A�J�E���g�̃`���b�g�V�X�e���ɂ�����Spring�����WebFlux�̊��p����
## �T�v
* LINE�����A�J�E���g�̃`���b�g�V�X�e���uLINE�`���b�g�v���\�z����ɂ�����ASpring WebFlux�𗘗p�����B���̗��_�Ɠ������ɍ��������Ƃ��Љ�B

### ���̃Z�b�V�����Ɋ֘A����L�[���[�h
* Spring WebFlux
* Spring WebMVC
* Server Sent Events

## �Z�b�V�����̓��e
### �b�̗���
LINE�����A�J�E���g�Ƃ͊��/���ƎҌ����ɊJ�݉\��LINE�A�J�E���g�ł���B
�u���[�h�L���X�g��^�[�Q�e�B���O�z�M�Ȃǂ̋@�\��L���Ă���A��ʃ��[�U�����̃`���b�g�V�X�e���Ƃ͈قȂ�uLINE�`���b�g�v�𗘗p����LINE�����A�J�E���g�ƈ�ʃ��[�U���`���b�g�ł���B

�`���b�g�V�X�e���Ƃ��āuLINE�`���b�g�v�ɋ��߂�����͈̂ȉ��̂��̂ł���B
* ���A���^�C���ʐM
* ���[�U����N���̃R�A�r�W�l�X���W�b�N
  * ���b�Z�[�W�̑��M�A�ߋ����b�Z�[�W�̎擾�A���b�Z�[�W�̌����A�e��f�[�^��CRUD����Ȃ�
* LINE���[�U�����M�����摜�E����E�����̎擾

#### ���A���^�C���ʐM
���A���^�C���ʐM�̎����ɂ��ẮA���������ɂ�Polling�ALong Polling�AServer Sent Events�iSSE�j�AWebSocket��4���ƂȂ��Ă������ASSE���̗p���邱�Ƃɂ����B
�܂��A�̗p����Spring WebFlux��Non Blocking��Web�t���[�����[�N�ł���B
1Request=1Thread��Servlet API�ƈقȂ�A1��Event Loop Tread�ŕ�����Request�����΂��邱�Ƃ���A�T�[�o�䐔���팸�ł���_�����������B

#### ���[�U����N���̃R�A�r�W�l�X���W�b�N
���[�U����N���̃R�A�r�W�l�X���W�b�N�̎����ɂ��ẮASpring WebMVC���̗p�����B
�J��������Spring Boot 2.0.0.M1�������Ƃ������Ƃ����������A�����Ȃ�Spring WebFlux��S�K�p����̂ł͂Ȃ��A�������Ċm���Ɍ����Ă���R���|�[�l���g���玎���Ă������Ƃ������f���������B

#### LINE���[�U�����M�����摜�E����E�����̎擾
LINE���[�U�����M�����摜�E����E�����̎擾�ɂ��ẮASpring WebFlux���̗p�����B
�摜�E����E�����t�@�C���͎Г�CDN�ɂ���A�F�ؓ��̎���ɂ��A�J�E���g�I�[�i�[�ɃA�N�Z�X������ɂ�Proxy���|����K�v�����������ASpring WebFlux��Content Proxy�ɂ��X���b�h���L���邱�ƂȂ�Proxy���|���邱�Ƃ��ł����B

�ȉ��͊J�����ɍ������_��m�E�n�E��Tips
* Netty OOME�̔����iNetty�ɕ񍐁E���C�m�F�ρj
* Blocking DNS Resolver
* Reactor hooks with MDC
* BlockHound

#### Netty OOME�̔����iNetty�ɕ񍐁E���C�m�F�ρj
WebFlux Client��p�����R�[�h��API call��timeout����ʂɔ��������OutOfMemoryError�iOOME�j�ŃT�[�o�������邱�Ƃ��������B
Netty��ReadTimeoutException���V���O���g���ł��������߁AReadTimeoutException���ێ����Ă���suppressedExceptions(List<Throwable>)����剻����OOME�����������B
���݂�Issue���N�[���ďC���ςł���B

�Q�l�F
```
https://github.com/netty/netty/pull/9152
```

#### Blocking DNS Resolver
Reactor Netty�ł�InetSocketAddress���g����Blocking DNS Resolver���g�p���Ă���̂ŁANetty��Non-Blocking DNS Resolver���g���悤�ɕύX����K�v������B
�܂��A�����܂��Tread���n���O���Ă��܂����ۂ�����������Issue���N�[�ρB�i����0.10.x����Backlog�j
�܂��A�f�t�H���g��Non-Blocking DNS Resolver�Ƃ���Ă�Reactor Netty�ɏオ���Ă���B

�Q�l�F
```
https://github.com/reactor/reactor-netty/issues/710
https://github.com/reactor/reactor-netty/issues/569
```

#### Reactor hooks with MDC
Servlet�̂悤��MDC��Request����n���ă��O�ɏo�͂�������������WebFlux��Event Loop Thread�Ƒ��������������B
WebFilter�AReactor Context�AHooks�����p���ĉ��Ƃ����������B
* WebFilter��Request����Context�ɓ����
* �e��onXXXX���\�b�h�ionSubscribe�Ȃǁj�̃��b�p�[���\�b�h����������
* ���b�p�[���\�b�h��Context����WebFilter�œo�^����Request�����擾����
* onXXXX���\�b�h���Ăяo���O��MDC#put�����s���A�ďo�����MDC#remove����
* ���b�p�[���\�b�h�����p�����悤��Hooks�ɓo�^����

#### BlockHound
BlockHound��Non Blocking�X���b�h�ɂ�����Blocking�R�[�������o���Ă����Java�G�[�W�F���g�B
���܂���{��Ō��y����Ă���L�����������Ȃ������̂ŏЉ�B
Gradle��Maven�ňˑ��֌W�ɒǉ����A�e�X�g�P�[�X�ɂ�BlockHound��L�������Ă����ƁA�e�X�g�P�[�X���s����Blocking�R�[������������Error���o�͂��Ă����B
���΂��Η\�����Ȃ�Blocking�R�[��������A�R�[�h���r���[�ŘR��邱�Ƃ�����̂œ����������߂���B

### �l�@��������
* �`���b�g�V�X�e���S�̂ɑ΂���Spring WebFlux��S�̗p�����A���������őΉ������Ă������f�͐������Ǝv�����B
�m���Ɍ�������Spring Boot�̃o�[�W������M1�������Ƃ����̂����邾�낤���A�m�E�n�E�𒙂߂�Ӗ����܂߂Ă�����x�K�͂��i���ĐV�Z�p�������Ă����͔̂�r�I���S�̂͂��B
* Non Blocking I/O�͂��܂������ł���΂��낢���CPU��ߖ񂵂��������\�̂悤�Ɏv�����B���ۃZ�b�V������Ɍy���������|�����Ƃ��냊�A�N�e�B�u�v���O���~���O���q�b�g�����B
�񓯊������ő҂����Ԃ������悭�����Ƃ������z��Spring WebFlux��Event Loop Tread��������Request���J����͓̂����悤�Ȏd�g�݂��낤�B

## �u�`����URL
https://speakerdeck.com/line_developers/examples-of-using-spring-and-webflux-in-the-chat-system-for-line-official-accounts
